<template>
  {{ selectedIndex }}
  <div class="h-screen bg-green-100" @click.self="deselectAll">
    <BraveDraggable
      v-for="(config, index) in multiConfig"
      class="hover-blue"
      @update:width="updatedHeightWidth(index)"
      @update:height="updatedHeightWidth(index)"
      :toggleSelectDeselect="false"
      @selected="selectedIndex = index"
      :selected="selectedIndex == index"
      :automaticDocking="false"
      :rotatable="true"
      v-model:width="config.width"
      v-model:height="config.height"
      :reSizable="true"
      :visible="config.visible"
      :position="config.position"
      :placement="config.placement"
      v-slot="{ hide, onHandleMouseDown, onHandleTouchMove, onHandleTouchEnd }"
      @placement-change="config.placement = $event"
      @position-change="config.position = $event"
    >
      <div
        @mousedown="onHandleMouseDown"
        @touchstart="onHandleMouseDown"
        @touchmove="onHandleTouchMove"
        @touchend="onHandleTouchEnd"
        :style="{ cursor: config.editable ? '' : 'default' }"
      >
        <BraveContentEditable
          @mousedown="captureEvent(index, $event)"
          @touchstart="captureEvent(index, $event)"
          @touchmove="captureEvent(index, $event)"
          @touchend="captureEvent(index, $event)"
          v-draglessClick="[makeEditable, index]"
          :style="{
            width: config.width,
            height: config.height,
            fontSize: config.fontSize,
            fontFamily: config.fontFamily,
          }"
          tag="div"
          ref="editable"
          :contenteditable="config.editable"
          v-model="config.text"
          @update:modelValue="findHeightforNewText(index)"
          :noNL="false"
          :noHTML="false"
        />
      </div>
    </BraveDraggable>
  </div>
</template>

<script>
import { DraglessClick } from 'bravevue';
import _ from 'lodash';
console.log(DraglessClick);
export default {
  directive: {
    draglessClick: DraglessClick,
  },
  // watch: {
  //   config: {
  //     deep: true,
  //     handler: function (newValue, oldValue) {
  //       console.log(newValue, oldValue);
  //       if (
  //         oldValue.height != newValue.height ||
  //         oldValue.width != newValue.width
  //       ) {
  //         console.log('shishir');
  //         this.config.fontSize = this.getOptimalFontSize(
  //           this.config.fontFamily,
  //           this.config.text,
  //           parseInt(this.config.height),
  //           parseInt(this.config.width)
  //         );
  //       }
  //       if (
  //         oldValue.fontSize != newValue.fontSize ||
  //         oldValue.fontFamily != newValue.fontFamily
  //       ) {
  //         // (fontStyle, text, width, fontSize)

  //         var newHeight =
  //           this.getOptimalFontSize1(
  //             this.config.fontFamily,
  //             this.config.text,
  //             parseInt(this.config.width),
  //             parseInt(this.config.fontSize)
  //           ) + 'px';
  //         console.log(newHeight);
  //         this.config.height = newHeight;
  //       }
  //     },
  //   },
  //   selectedIndex: {
  //     handler: function (newValue, oldValue) {
  //       if (this.selectedIndex != null) {
  //         this.config = this.multiConfig[this.selectedIndex];
  //         console.log('config', this.config);
  //       }
  //     },
  //   },
  // },
  data() {
    return {
      selectedIndex: null,
      multiConfig: [
        {
          fontFamily: 'Ariel',
          editable: false,
          text: 'hello World\n multi line text',
          fontSize: '20px',
          visible: true,
          width: '200px',
          height: '100px',
          position: {
            top: '10px',
            right: '10px',
          },
          placement: 'top-right',
        },
        {
          fontFamily: 'Ariel',
          editable: false,
          text: 'hello World\n multi line text',
          fontSize: '20px',
          visible: true,
          width: '200px',
          height: '100px',
          position: {
            top: '10px',
            left: '10px',
          },
          placement: 'top-left',
        },
      ],

      config: {
        fontFamily: 'Ariel',
        text: 'hello World\n multi line text',
        fontSize: '20px',
        visible: true,
        width: '200px',
        height: '100px',
        position: {
          top: '10px',
          right: '10px',
        },
        placement: 'top-right',
      },
    };
  },
  methods: {
    updatedHeightWidth(index) {
      console.log('did you call me');
      this.multiConfig[index].fontSize = this.getOptimalFontSize(
        this.multiConfig[index].fontFamily,
        this.multiConfig[index].text,
        parseInt(this.multiConfig[index].height),
        parseInt(this.multiConfig[index].width)
      );
    },
    findHeightforNewText(index) {
      var newHeight = this.getOptimalFontSize1(
        this.multiConfig[index].fontFamily,
        this.multiConfig[index].text,
        parseInt(this.multiConfig[index].width),
        parseInt(this.multiConfig[index].fontSize)
      );
      console.log(newHeight);
      this.multiConfig[index].height = newHeight;
    },
    deselectAll() {
      this.multiConfig[this.selectedIndex].editable = false;
      this.selectedIndex = null;
    },
    captureEvent(index, e) {
      console.log('myindex', index);
      if (this.multiConfig[index].editable) {
        e.stopPropagation();
      }
    },
    makeEditable(index, event) {
      if (index == this.selectedIndex) {
        this.multiConfig[index].editable = true;
        setTimeout(function () {
          const range = document.createRange();
          const position = document.caretRangeFromPoint(
            event.clientX,
            event.clientY
          ); // or use document.caretRangeFromPoint(event.clientX, event.clientY);

          var textNode = position.startContainer;
          var offset = position.startOffset;
          range.setStart(textNode, offset);
          range.collapse(true);

          const selection = window.getSelection();
          selection.removeAllRanges();
          selection.addRange(range);
        }, 100);
      }
    },
    testingfun() {
      alert('shishir234s');
    },
    getOptimalFontSize1: function (fontStyle, text, width, fontSize) {
      console.log('wwww', fontSize);
      let tempDiv = document.createElement('div');
      tempDiv.style.fontFamily = fontStyle;
      tempDiv.style.fontSize = parseInt(fontSize) + 'px';
      tempDiv.style.position = 'fixed';
      tempDiv.style.top = '0px';
      tempDiv.style.left = '0px';
      tempDiv.style.zIndex = '10000';
      tempDiv.innerHTML = text;
      tempDiv.style.width = width + 'px';
      document.body.appendChild(tempDiv);
      var height = tempDiv.offsetHeight;

      tempDiv.remove();
      console.log('height', height, fontSize);
      return height + 'px';
    },
    getOptimalFontSize: _.throttle(function (fontType, text, height, width) {
      let testDiv = document.createElement('div');
      testDiv.style.fontFamily = fontType;
      testDiv.style.position = 'absolute';
      testDiv.style.top = '0px';
      testDiv.style.left = '0px';
      testDiv.style.zIndex = '10000';

      console.log(text);
      testDiv.innerHTML = text;
      testDiv.style.visibility = 'hidden';
      // testDiv.style.height = height + 'px';
      testDiv.style.width = width + 'px';
      document.body.appendChild(testDiv);

      // let fontSize = 8;
      // testDiv.style.fontSize = fontSize + 'px';
      // while (
      //   (testDiv.offsetHeight < height || testDiv.offsetWidth < width) &&
      //   fontSize < 1000
      // ) {
      //   console.log(fontSize);
      //   fontSize += 1;
      //   testDiv.style.fontSize = fontSize + 'px';
      // }

      // fontSize -= 1;

      // new logic
      let minFontSize = 1;
      let maxFontSize = 500;
      let fontSize;

      while (minFontSize <= maxFontSize) {
        console.log('test');
        fontSize = Math.floor((minFontSize + maxFontSize) / 2);
        testDiv.style.fontSize = fontSize + 'px';

        if (testDiv.offsetHeight >= height && testDiv.offsetWidth >= width) {
          maxFontSize = fontSize - 1;
        } else {
          minFontSize = fontSize + 1;
        }
      }

      fontSize = maxFontSize;

      console.log('final font size', fontSize);
      testDiv.style.fontSize = fontSize + 'px';

      testDiv.remove();

      return fontSize + 'px';
    }, 50),
  },
};
</script>
<style>
.hover-blue:hover {
  outline: 2px solid purple;
}
</style>
