<template>
  <div class="wrap">
    <h3>SA Arm controls</h3>
    <div class="controls">
      <Checkbox
        ref="arm-enabled"
        :name="'SA Arm Enabled'"
        @toggle="updateArmEnabled($event)"
      />
    </div>
  </div>
</template>

<script>
import ROSLIB from "roslib";
import Checkbox from "./Checkbox.vue";

let interval;
// In seconds
const updateRate = 0.1;

export default {
  components: {
    Checkbox
  },
  data() {
    return {
      arm_enabled: false,
      joystick_pub: null
    };
  },

  beforeDestroy: function () {
    window.clearInterval(interval);
  },

  created: function () {
    this.joystick_pub = new ROSLIB.Topic({
      ros: this.$ros,
      name: "/xbox/sa_control",
      messageType: "sensor_msgs/Joy"
    });
    interval = window.setInterval(() => {
      if (this.arm_enabled) {
        const gamepads = navigator.getGamepads();
        for (let i = 0; i < 4; i++) {
          const gamepad = gamepads[i];
          if (
            (gamepad && gamepad.id.includes("Microsoft")) ||
            gamepad.id.includes("Xbox")
          ) {
            let buttons = gamepad.buttons.map((button) => {
              return button.value;
            });

            const joystickData = {
              axes: gamepad.axes,
              buttons: buttons
            };
            var joystickMsg = new ROSLIB.Message(joystickData);
            this.joystick_pub.publish(joystickMsg);
          }
        }
      }
    }, updateRate * 1000);
  },

  methods: {
    updateArmEnabled: function (enabled) {
      this.arm_enabled = enabled;
    }
  }
};
</script>

<style scoped>
.wrap {
  display: inline-block;
  align-items: center;
  justify-items: center;
  width: 100%;
}
.controls {
  display: flex;
  align-items: center;
}
.header {
  display: flex;
  align-items: center;
}
.joint-b-calibration {
  display: flex;
  gap: 10px;
  width: 250px;
  font-weight: bold;
  color: red;
}
</style>
