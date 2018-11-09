<template>
	<div id="scrollPane" class="scroll-pane" ref="scrollPane" @wheel.prevent="wheel"> 
		<div class="scroll-content">
		<slot></slot>
		</div>
		<div class="scroll-pane-bar" @mousemove="move">
			<svg height="100%" width="12px">
				<rect height="100%" width="12px" :fill="barBackground"
					@click="clickBg"
				></rect>
				<rect
					ref="track"
					width="12px"
					:height="trackHeight"
					:fill="trackColor"
					v-if="proportion < 1"
					:y="clientY"
					style="cursor: pointer; opacity: 0.5;"
					rx="2px"
					ry="2px"
					@mousedown.prevent.stop="scrolling = true"
					@mouseup.prevent.stop="scrolling = false"
					@mouseout.prevent.stop="scrolling = false"
				></rect>
			</svg>
		</div>
	</div>
</template>


<script>
export default {
  name: 'scrollPane',
  props: {
    barBackground: {
      type: String,
      default: '#F0F0F0',
    },
    trackColor: {
      type: String,
      default: '#666',
    },
  },
  data() {
    return {
      proportion: 0,
      scrolling: false,
      clientY: 0,
    };
  },
  mounted() {
    this.proportion = window.innerHeight / this.$refs.scrollPane.clientHeight;
  },
  methods: {
		wheel(evt) {
      const height = this.$refs.track.height.baseVal.value;

			if (this.clientY + evt.deltaY <= 0) {
				this.clientY = 0;
				return;
			}
			if (this.clientY + evt.deltaY + height > window.innerHeight) {
				this.clientY = window.innerHeight - height;
				return;
			}
			this.clientY += evt.deltaY;
			this.$el.querySelector('.scroll-content').scrollTop = 1230;
		},
    clickBg(evt) {
      this.scrolling = true;
      this.move(evt);
      this.scrolling = false;
    },
    move(evt) {
      if (this.scrolling) {
        const height = this.$refs.track.height.baseVal.value;
        setTimeout(() => {
          if (evt.clientY + height > window.innerHeight) {
            this.clientY = window.innerHeight - height;
          } else {
            this.clientY = Math.round(evt.clientY - height / 2);
          }
        }, 0);
      }
    },
  },
  computed: {
    trackHeight() {
      return `${parseInt(
        ((window.innerHeight * this.proportion) / window.innerHeight) * 100,
        10,
      )}%`;
    },
  },
};
</script>

<style lang="scss">
.scroll-pane {
  width: 100%;
  max-width: 100vw;
  position: relative;
  overflow: hidden;
  .scroll-content {
    width: 100%;
    height: auto;
    overflow: hidden;
  }
  .scroll-pane-bar {
    height: 100vh;
    max-height: 100vh;
    width: 12px;
    position: absolute;
    right: 0;
    top: 0;
  }
}
</style>
