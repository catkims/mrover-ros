<template>
  <div>
    <DriveControls></DriveControls>
    <ArmControls></ArmControls>
    <GimbalControls></GimbalControls>
    <JointStateTable
      :joint-state-data="jointState"
      :vertical="true"
    ></JointStateTable>
    <MoteusStateTable :moteus-state-data="moteusState"></MoteusStateTable>
  </div>
</template>

<script>
import ROSLIB from "roslib";
import DriveControls from "./DriveControls.vue";
import ArmControls from "./ArmControls.vue";
import GimbalControls from "./GimbalControls.vue";
import JointStateTable from "./JointStateTable.vue";
import MoteusStateTable from "./MoteusStateTable.vue";

export default {
  components: {
    DriveControls,
    ArmControls,
    GimbalControls,
    JointStateTable,
    MoteusStateTable,
  },
  data() {
    return {
      jointState: {},
      moteusState: {},
    };
  },

  created: function () {
    this.brushless_motors = new ROSLIB.Topic({
      ros: this.$ros,
      name: "drive_status",
      messageType: "mrover/MotorsStatus",
    });

    this.brushless_motors.subscribe((msg) => {
      this.jointState = msg.joint_states;
      this.moteusState = msg.moteus_states;
    });
  },
};
</script>
