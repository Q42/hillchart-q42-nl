<template>
  <div>
    <transition name="popup-fade">
      <div v-show="showCopy" id="popup">
        <p>Copied to clipboard</p>
        <img id="copy" />
      </div>
    </transition>
    <div id="container">
      <p class="copy">Click to copy</p>
      <canvas
        id="hillchart"
        @mousemove="e => doDrag(e)"
        @click="copyToClipBoard"
      ></canvas>
    </div>
  </div>
</template>

<script>
// define the imaginary circle for defining the arcs on
const circle = {
  x: 150,
  y: 240,
  r: 200
};

function loadImage(url) {
  return new Promise(function(resolve) {
    const image = new Image();
    image.onload = function() {
      resolve(image);
    };
    image.src = url;
  });
}

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

    this.cyclist = loadImage('cyclist.png');
    this.award = loadImage('award.png');

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

    async drawHillChart2(progress) {
      this.canvas.width = this.canvas.width; // this resets the canvas
      const angleOffset = 45; // we don't start and end | but / and \
      const startAngle = -180 + angleOffset;
      const endAngle = -angleOffset;
      const angularDiffOfTotal = endAngle - startAngle;
      const doneAngle = startAngle + progress * angularDiffOfTotal;

      this.drawArc(startAngle, doneAngle, '#000');
      this.drawArc(doneAngle, endAngle, 'lightgrey');

      const cyclist = await this.cyclist;
      // the icon should be ontop of the arc so r is bigger
      const point = polarToCartesian(
        circle.x,
        circle.y,
        circle.r + 30,
        doneAngle + 90
      );
      const angle = ((90 + doneAngle) / 180) * Math.PI;
      this.drawMarker(cyclist, point, angle);

      if (progress > 0.9) {
        this.drawAward();
      }
    },

    drawArc(a1, a2, style) {
      this.ctx.lineWidth = 2;
      this.ctx.strokeStyle = style;
      this.ctx.beginPath();
      this.ctx.arc(
        circle.x,
        circle.y,
        circle.r,
        (a1 / 180) * Math.PI,
        (a2 / 180) * Math.PI,
        false
      );
      this.ctx.stroke();
    },
    drawMarker(marker, point, angleInRadians) {
      const dx = marker.width / 2;
      const dy = marker.height / 2;
      const x = point.x - dx / 2;
      const y = point.y - dy / 2;

      this.ctx.translate(x + dx / 2, y + dy / 2);
      this.ctx.rotate(angleInRadians);
      this.ctx.drawImage(marker, -dx / 2, 0, dx, dy);
      this.ctx.rotate(-angleInRadians);
      this.ctx.translate(-x - dx / 2, -y - dy / 2);
    },

    async drawAward() {
      // fade out chart
      this.ctx.fillStyle = 'rgba(255,255,255,0.6)';
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

      const award = await this.award;
      this.ctx.drawImage(
        award,
        this.canvas.width / 2 - award.width / 8,
        0,
        award.width / 4,
        award.height / 4
      );
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

#hillchart:hover {
  cursor: grab;
}

#container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
  color: grey;
}

#container .copy {
  opacity: 0;
  transition: opacity 0.2s linear;
  transition-delay: 0.5s;
}

#container:hover .copy {
  opacity: 1;
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
