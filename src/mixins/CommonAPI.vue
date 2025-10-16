<script>
import BarcodeDetector, { setZXingModuleOverrides } from "barcode-detector"

export default {
  beforeMount () {
    // if (!('BarcodeDetector' in window)) {
    window.BarcodeDetector = BarcodeDetector
    // }
  },
  props: {
    wasmPath: {
      default: ''
    }
  },
  mounted () {
    if (this.wasmPath) {
      setZXingModuleOverrides({
        locateFile: (path, prefix) => {
          if (path.endsWith(".wasm")) {
            return this.wasmPath
          }
          return prefix + path;
        },
      });
    }
  },

  methods: {
    async onDetect (resultPromise) {
      this.$emit("detect", resultPromise);

      try {
        const { content } = await resultPromise;

        if (content !== null) {
          this.$emit("decode", content);
        }
      } catch (error) {
        // fail silently
      }
    }
  }
};
</script>
