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
      <canvas id="hillchart" @mousemove="doDrag" @click="copyToClipBoard"></canvas>
    </div>
  </div>
</template>

<script>
const cross = new Image();
cross.src = "cross.png"
export default  {
  props: {
    progress: Number
  },
  data() {
    return  { showCopy: false };
  },
  mounted() {
    drawHillChart2(this.$props.progress/100);
  },

  methods: {
    doDrag(e) {
      const rect = e.target.getBoundingClientRect();
      const dx = e.clientX - rect.left;
      const p = Math.min(1, Math.max(0, dx / rect.width));
      drawHillChart2(p)
    },
    
    async copyToClipBoard() {
      
      this.showCopy = true;
      const canvas = document.querySelector('canvas');
      const promise = new Promise((resolve)=>{
        canvas.toBlob((blob)=> resolve(blob));
      });

      const blob = await promise;
      const item = new ClipboardItem({ "image/png": blob });
      navigator.clipboard.write([item]); 
      const image = document.getElementById("copy");
      image.src = URL.createObjectURL(blob);
      setTimeout(()=> this.showCopy = false, 1000 );
    }
  },
}

function drawHillChart2(progress) {
  const canvas = document.querySelector('canvas');
  const ctx = canvas.getContext('2d');
  
  canvas.width = canvas.width;
  const angleOffset = 45; // we don't start and end | but / and \
  const startAngle = -180+angleOffset;
  const endAngle = - angleOffset
  const angularDiffOfTotal = endAngle - startAngle;
  const doneArc = startAngle + progress * angularDiffOfTotal;

  ctx.lineWidth = 2;
  ctx.beginPath();
  const x = 150;
  const y = 240;
  const r = 210;
  ctx.arc(x, y, r, startAngle/180*Math.PI, doneArc/180*Math.PI , false );
  ctx.stroke();

  ctx.strokeStyle = "lightgrey";
  ctx.beginPath();
  ctx.arc(x, y, r, doneArc/180*Math.PI, endAngle/180*Math.PI , false );
  ctx.stroke();

  const crossPoint = polarToCartesian(x, y, r, doneArc+90);
  const dx = cross.width/4;
  const dy = cross.height/4;
  ctx.drawImage(cross, crossPoint.x-dx/2, crossPoint.y-dy/2, dx, dy)
}

function polarToCartesian(centerX, centerY, radius, angleInDegrees) {
  var angleInRadians = (angleInDegrees-90) * Math.PI / 180.0;

  return {
    x: centerX + (radius * Math.cos(angleInRadians)),
    y: centerY + (radius * Math.sin(angleInRadians))
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
  top:50%;
  left:50%;
  transform: translate3d(-50%,-50%,0);
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
  top:50%;
  left:50%;
  transform: translate3d(-50%,-50%,0);
  background: #ffd;
  padding: 10px;
  /* border: dashed; */
}

.popup-fade-enter-active {
  transition: all .3s ease;
}
.popup-fade-leave-active {
  transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
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
