<template>
  <div></div>
</template>

<script>
export default {
  name: "Megatimer",
  props: {
    id: {
      required: true,
      type: String,
    },
  },
  data() {
    return {
      timerElementId: "",
      timerElement: null,
      timerData: {},
    };
  },
  created() {
    this.loadTimerData();
  },
  methods: {
    createTimerElement() {
      this.timerElementId = this.id;
      while (document.getElementById("timer" + this.timerElementId)) {
        this.timerElementId = this.timerElementId + "0";
      }

      this.timerElement = document.createElement("div");
      this.timerElement.id = "timer" + this.timerElementId;
      this.timerElement.style.minWidth = this.timerData.width + "px";
      this.timerElement.style.height = this.timerData.height + "px";

      this.$el.appendChild(this.timerElement);
    },
    loadTimerData() {
      const xhr = new XMLHttpRequest();
      const url = `//megatimer.ru/data/${this.id}.json`;

      xhr.open("GET", url, true);
      xhr.responseType = "json";
      xhr.onload = () => {
        const status = xhr.status;
        if (status === 200) {
          this.timerData = xhr.response;
          this.onTimerDataLoaded();
        }
      };
      xhr.send();
    },
    onTimerDataLoaded(timerData) {
      this.createTimerElement();
      this.loadTimerLibrary();
    },
    loadTimerLibrary() {
      var script = document.createElement("script");
      script.src = "//megatimer.ru/timer/timer.min.js?v=1";
      script.onload = this.onTimerLibraryLoad;
      var parentElement =
          document.head || document.getElementsByTagName("head")[0];
      parentElement.appendChild(script);
    },
    onTimerLibraryLoad() {
      const megaTimer = new window.MegaTimer(
          this.timerElementId,
          this.timerData
      );
      megaTimer.run();
    },
  },
};
</script>
