<template>
  <select v-model="fontSize">
    <option value="10px">10</option>
    <option value="20px">20</option>
    <option value="30px">30</option>
    <option value="40px">40</option>
    <option value="50px">40</option>
  </select>
</template>
<script>
export default {
  props : {
    modelValue : Object,
  }
  ],
  watch :{
    config : {
      deep:true,
      handle : function(){
        tempConfig = config;
        fontSize = config.fontSize;
      }
    }
    fontSize(){
      // finding the new height.
      var height = getHeight(this.tempConfig.fontFamily, this.tempConfig.text, this.tempConfig.width, this.tempConfig.fontSize);
      this.tempConfig.height = height;
    }
  }
  data() {
    return {
      fontSize : config.fontSize;
      tempConfig: {
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
      tempConfig: config
    };
  },
  methods: {
    getHeight: function (fontFamily, text, width, fontSize) {
      let tempDiv = document.createElement('div');
      tempDiv.style.fontFamily = fontFamily;
      tempDiv.style.fontSize = fontSize;
      tempDiv.innerText = text;
      tempDiv.style.width = width + 'px';
      document.body.appendChild(tempDiv);
      var height = tempDiv.offsetHeight;
      tempDiv.remove();
      console.log('height', height, fontSize);
      return height + 'px';
    }
  }
};
</script>
