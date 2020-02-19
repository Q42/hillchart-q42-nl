<template>
  <div>
    <transition name="popup-fade">
      <div v-show="showCopy" id="popup">
        <p>Copied to clipboard</p>
        <img id="copy" />
      </div>
    </transition>
    <div id="container">
      <p>Click to copy</p>
      <canvas
        id="hillchart"
        @mousemove="e => doDrag(e)"
        @click="copyToClipBoard"
      ></canvas>
    </div>
  </div>
</template>

<script>
const cyclist = new Image();

export default {
  props: {
    progress: Number
  },
  data() {
    return { showCopy: false };
  },
  async mounted() {
    this.canvas = document.querySelector('canvas');
    this.ctx = this.canvas.getContext('2d');
    const def = new Promise(function(resolve) {
      cyclist.onload = function() {
        resolve(cyclist);
      };
    });
    cyclist.src = 'cyclist.png';
    const cyclistLoaded = await def;

    this.drawHillChart2(this.$props.progress / 100);
  },

  methods: {
    doDrag(e) {
      const rect = e.target.getBoundingClientRect();
      const dx = e.clientX - rect.left;
      const p = Math.min(1, Math.max(0, dx / rect.width));
      this.drawHillChart2(p);
    },

    async copyToClipBoard() {
      this.showCopy = true;
      const canvas = document.querySelector('canvas');
      const blob = await new Promise(resolve => canvas.toBlob(b => resolve(b)));

      const item = new ClipboardItem({ 'image/png': blob });
      navigator.clipboard.write([item]);
      const image = document.getElementById('copy');
      image.src = URL.createObjectURL(blob);
      setTimeout(() => (this.showCopy = false), 1000);
    },

    drawHillChart2(progress) {
      this.canvas.width = this.canvas.width;
      const angleOffset = 45; // we don't start and end | but / and \
      const startAngle = -180 + angleOffset;
      const endAngle = -angleOffset;
      const angularDiffOfTotal = endAngle - startAngle;
      const doneArc = startAngle + progress * angularDiffOfTotal;

      this.ctx.lineWidth = 2;
      this.ctx.beginPath();

      // define the imaginary circle for defining the arcs on
      const x = 150;
      const y = 240;
      const r = 200;
      this.ctx.arc(
        x,
        y,
        r,
        (startAngle / 180) * Math.PI,
        (doneArc / 180) * Math.PI,
        false
      );
      this.ctx.stroke();

      this.ctx.strokeStyle = 'lightgrey';
      this.ctx.beginPath();
      this.ctx.arc(
        x,
        y,
        r,
        (doneArc / 180) * Math.PI,
        (endAngle / 180) * Math.PI,
        false
      );
      this.ctx.stroke();

      // the icon should be ontop of the arc so r is bigger
      const icon = polarToCartesian(x, y, r + 30, doneArc + 90);
      this.drawImage(icon, ((90 + doneArc) / 180) * Math.PI);
    },
    drawImage(point, angleInRadians) {
      const dx = cyclist.width / 2;
      const dy = cyclist.height / 2;
      const x = point.x - dx / 2;
      const y = point.y - dy / 2;

      // this.ctx.drawImage(cyclist, x, y, dx, dy)

      this.ctx.translate(x + dx / 2, y + dy / 2);
      this.ctx.rotate(angleInRadians);
      this.ctx.drawImage(cyclist, -dx / 2, 0, dx, dy);
      this.ctx.rotate(-angleInRadians);
      this.ctx.translate(-x - dx / 2, -y - dy / 2);
    }
  }
};

function polarToCartesian(centerX, centerY, radius, angleInDegrees) {
  var angleInRadians = ((angleInDegrees - 90) * Math.PI) / 180.0;

  return {
    x: centerX + radius * Math.cos(angleInRadians),
    y: centerY + radius * Math.sin(angleInRadians)
  };
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hillchart {
  width: 400px;
  height: 200px;
  border: 1px solid red;
}
#hillchart {
  width: 400px;
  height: 200px;
}
#container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
}
path {
  user-select: none;
  pointer-events: none;
}
#copy {
  width: 400px;
}
#popup {
  z-index: 1;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
  background: #ffd;
  padding: 10px;
  /* border: dashed; */
}

.popup-fade-enter-active {
  transition: all 0.3s ease;
}
.popup-fade-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}
.popup-fade-enter
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: scale(1.1);
  opacity: 0;
}

.popup-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
