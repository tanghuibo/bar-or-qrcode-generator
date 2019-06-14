<template>
  <div id="app">
    <el-container>
      <el-aside width="70%">
        <codemirror
          @cursorActivity="cursorActivity"
          ref="myCm"
          v-model="code"
          :options="{
            tabSize: 4,
            mode: 'text/javascript',
            theme: 'eclipse',
            lineNumbers: true,
            line: true
          }"
          style="padding-right: 30px"
        ></codemirror>
      </el-aside>
      <el-container>
        <el-main id="right-view">
          <div>
            <div
              :style="{'text-align': 'center', 'height': '200px',  'line-height': showQr ? '200px' : (rightViewHeight + 'px'), 'margin': 'auto'}"
            >
              <el-tooltip
                v-if="showBar"
                class="item"
                effect="dark"
                :content="codeValue"
                placement="left"
              >
                <barcode :value="codeValue" displayValue="false">
                  <span
                    style="padding: 15px 40px; background-color: #fef0f0; border-radius: 6px; color: #f56c6c; font-size: 30px;"
                  >条形码不支持中文</span>
                </barcode>
              </el-tooltip>
            </div>
            <div v-if="showQr" style="text-align: center; margin: auto;">
              <el-tooltip class="item" effect="dark" :content="codeValue" placement="left">
                <qrcode :value="codeValue" :size="size" level="H"/>
              </el-tooltip>
            </div>
          </div>
        </el-main>
        <el-footer>
          <div>
            <el-row style="text-align: center;">
              <el-col :span="12">
                <el-switch v-model="showQr" active-text="显示二维码"></el-switch>
              </el-col>
              <el-col :span="12">
                <el-switch v-model="showBar" active-text="显示条形码"></el-switch>
              </el-col>
            </el-row>
          </div>
        </el-footer>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import barcode from "vue-barcode";
import qrcode from "qrcode.vue";
export default {
  name: "app",
  components: {
    barcode,
    qrcode
  },
  data() {
    return {
      codeValue: "",
      showQr: true,
      showBar: true,
      size: 300,
      rightViewHeight: 500,
      code: "鼠标选中文本(例如: BH89757)获取二维码和条形码，注意条形码不支持中文哟～"
    };
  },
  mounted() {
    let rightView = document.getElementById("right-view");
    this.size = rightView.clientWidth - 50;
    this.rightViewHeight = rightView.clientHeight - 100;

    /**
     * 监听浏览器尺寸改变
     */
    window.onresize = () => {
      this.size = rightView.clientWidth - 50;
      this.rightViewHeight = rightView.clientHeight - 100;
    };

    /**
     * 选词
     */
    this.$refs.myCm.codemirror.setSelection(
      { line: 0, ch: 11 },
      { line: 0, ch: 18 }
    );
  },
  methods: {
    /**
     * 通过事件获取选词
     */
    cursorActivity(codemirror) {
      let value = codemirror.getSelection();
      if (value != null && value != "") {
        this.codeValue = value;
      }
    }
  }
};
</script>
