<script setup lang="ts">
import { World, Model, ThirdPersonCamera, Keyboard, Find, types, useSpring, Editor} from "lingo3d-vue"
import { ref, computed, onMounted } from "vue"
const pose = ref('idle');
const looking = ref(false);
const person= ref<types.Model>();
const personPosition = ref('Positions');
const record = ref(false);
const camera = ref<types.Camera>();
const cameraPosition = ref('Rotations');

const camY = computed(() => {
  if (looking.value) return 150
  return 50
});
const camYSpring = useSpring({to: camY, stiffness: 100, damping: 10});

const handleKeyPress = (key: string) => {
  if (key === 'w') {
    pose.value = 'walk'
    person.value?.moveForward(-10)
  };
  if (key === 's') {
    pose.value = 'walk'
    person.value?.moveForward(10)
  };
  if (key === 'a') {
    pose.value = 'walk'
    person.value?.moveRight(10)
  };
  if (key === 'd') {
    pose.value = 'walk'
    person.value?.moveRight(-10)
  };
  if (key === 'p') {
    alert(person.value?.x.toString() + " " + person.value?.y.toString() + " " + person.value?.z.toString())
  };
  if (key ==='Space') {
    // jump
    person.value?.moveTo(person.value?.x,person.value?.y + 0.5,person.value?.z, 20)
  }
}

const handleKeyUp = (key: string) => {
  if (key === 'w') {
    pose.value = 'idle'
  };
  if (key === 's') {
    pose.value = 'idle'
  };
  if (key === 'a') {
    pose.value = 'idle'
  };
  if (key === 'd') {
    pose.value = 'idle'
  };
  if (key === 'p') {
    looking.value = !looking.value
  };
}
onMounted(() => {
  addEventListener('keydown', (e) => {
    personPosition.value = personPosition.value = "Positions " + person.value?.x.toString()! + " " + person.value?.y.toString()! + "\n" + person.value?.z.toString()!;
    if (e.key === 'r') {
      record.value = true;
      alert('recording')
    };
    if (e.key === 't') {
      record.value = false;
      alert('stopped recording');
    };
  })
  // mouse click
  addEventListener('mousedown', (e) => {
    cameraPosition.value = "Rotations " + camera.value?.innerRotationY.toString()! + " " + camera.value?.y.toString()! + " " + camera.value?.z.toString()!;
  })
});
</script>

<template>
  <World default-light="studio" :default-light-scale="1" :bloom-strength="0.1" :bloom-radius="0.1" color="rgb(80, 80, 150)">
    <Model src="cinema.glb" :scale="10" physics="map">
      <Find 
        name="screen0"
        texture="walkers.mp4" 
        @mouse-over="looking = true"
        @mouse-out="looking = false"
      />
    </Model>
    <ThirdPersonCamera active mouse-control="true" :inner-z="100" :inner-y="camYSpring" ref="camera">
      <Model 
        src="person.fbx" 
        physics="character"
        ref="person"
        :animations="{
          idle: 'Idle.fbx',
          walk: 'Walking.fbx', 
        }"
        :animation="pose"
        pbr
      />
    </ThirdPersonCamera>
    <Keyboard @key-press="handleKeyPress" @key-up="handleKeyUp"/>
  </World>
  <div class="info">
    <div class="person">{{personPosition}}</div>
  </div>
</template>
<style scoped>
.person{
  z-index: 100;
  position: absolute;
  top: 0;
  left: 0;
  color: plum;
  font-size: 3rem;
  font-weight: 900;
  flex: 1;
}
.camera {
  z-index: 100;
  position: relative;
  top: 170px;
  right: 0;
  color: blueviolet;
  font-size: 3rem;
  font-weight: 900;
  flex: 2;
}
</style>