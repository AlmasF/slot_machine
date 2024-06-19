<script setup lang="ts"></script>

<template>
  <main>
    <div class="machine">
      <div>
        <div
          class="roll"
          ref="firstSection"
        >
          <span></span>
          <span style="
              background-image: url('../../images/marcile_happy.jpg');
              background-size: cover;
              background-repeat: no-repeat;
              background-position: center;
            "></span>
        </div>
      </div>
      <div>
        <div
          class="roll"
          ref="secondSection"
        >
          <span></span>
          <span style="
              background-image: url('../../images/marcile_happy.jpg');
              background-size: cover;
              background-repeat: no-repeat;
              background-position: center;
            "></span>
        </div>
      </div>
      <div>
        <div
          class="roll"
          ref="thirdSection"
        >
          <span></span>
          <span style="
              background-image: url('../../images/marcile_happy.jpg');
              background-size: cover;
              background-repeat: no-repeat;
              background-position: center;
            "></span>
        </div>
      </div>
    </div>
    <button
      @click="slotMachineAction"
      :disabled="isRunning == false && (firstTimer || secondTimer || thirdTimer)"
    >
      {{
        isRunning == false && (firstTimer || secondTimer || thirdTimer)
          ? 'Wait till stops'
          : isRunning
            ? 'Stop'
            : 'Start'
      }}
    </button>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'
const isRunning = ref(false)
const firstIterator = ref(0)
const secondIterator = ref(0)
const thirdIterator = ref(0)
const firstSection = ref<HTMLElement | null>(null)
const secondSection = ref<HTMLElement | null>(null)
const thirdSection = ref<HTMLElement | null>(null)
const firstActionTrigger = ref(0)
const secondActionTrigger = ref(0)
const thirdActionTrigger = ref(0)
const initSpeedOfRoll = 30
const firstSpeedOfRoll = ref(0)
const secondSpeedOfRoll = ref(0)
const thirdSpeedOfRoll = ref(0)
const firstSpeedOfSlowDown = ref(20)
const secondSpeedOfSlowDown = ref(20)
const thirdSpeedOfSlowDown = ref(20)
let firstTimer = ref<NodeJS.Timeout | null>(null)
let secondTimer = ref<NodeJS.Timeout | null>(null)
let thirdTimer = ref<NodeJS.Timeout | null>(null)

const rollSectionSpan = (secondSpan, actionTrigger, timer, speedOfRoll, doOnce = false) => {
  timer.value = setInterval(() => {
    const currentMarginTop = parseInt(secondSpan.style.marginTop)
    secondSpan.style.marginTop = currentMarginTop + speedOfRoll.value + 'px'
    if (currentMarginTop >= 0) {
      secondSpan.style.marginTop = '0px'
      actionTrigger.value++
    }
  }, 10)
}
const addSpanToTheTopOfSection = (section, iterator) => {
  const span = document.createElement('span')
  if (iterator.value % 4 == 0) {
    span.style.backgroundImage = 'url("../../images/marcile_happy.jpg")'
    span.style.backgroundSize = 'cover'
    span.style.backgroundRepeat = 'no-repeat'
    span.style.backgroundPosition = 'center'
  } else if (iterator.value % 3 == 0) {
    span.style.backgroundImage = 'url("../../images/marcile_saf.jpg")'
    span.style.backgroundSize = 'cover'
    span.style.backgroundRepeat = 'no-repeat'
    span.style.backgroundPosition = 'center'
  } else if (iterator.value % 2 == 0) {
    span.style.backgroundImage = 'url("../../images/marcille_off.jpg")'
    span.style.backgroundSize = 'cover'
    span.style.backgroundRepeat = 'no-repeat'
    span.style.backgroundPosition = 'center'
  } else {
    span.style.backgroundImage = 'url("../../images/marcille_disgust.jpg")'
    span.style.backgroundSize = 'cover'
    span.style.backgroundRepeat = 'no-repeat'
    span.style.backgroundPosition = 'center'
  }
  span.style.marginTop = '-500px'
  span.style.display = 'block'
  span.style.width = '100%'
  span.style.height = '100%'
  span.innerHTML = iterator.value.toString()
  iterator.value++
  section.value?.prepend(span)
  return span
}
const removeSpanFromTheBottomOfSection = (section) => {
  section.value?.lastElementChild?.remove()
}
const stopRoll = (timer) => {
  clearInterval(timer.value as NodeJS.Timeout)
  timer.value = null
}
const slowDownRoll = (speedOfRoll, timer, speedOfSlowDown) => {
  let slowDownInterval = setInterval(() => {
    if (speedOfRoll.value <= 0) {
      speedOfRoll.value = 0
      clearInterval(slowDownInterval)
      clearInterval(timer.value as NodeJS.Timeout)
      timer.value = null
    } else {
      speedOfRoll.value -= speedOfSlowDown
    }
  }, 100)
}
const addSpanAndRoll = (section, trigger, timer, iterator, speedOfRoll) => {
  const secondSpan = addSpanToTheTopOfSection(section, iterator)
  rollSectionSpan(secondSpan, trigger, timer, speedOfRoll)
}
const slotMachineAction = () => {
  if (isRunning.value) {
    slowDownRoll(firstSpeedOfRoll, firstTimer, firstSpeedOfSlowDown.value)
    slowDownRoll(secondSpeedOfRoll, secondTimer, secondSpeedOfSlowDown.value)
    slowDownRoll(thirdSpeedOfRoll, thirdTimer, thirdSpeedOfSlowDown.value)
  } else {
    startSlotMachine()
  }
  isRunning.value = !isRunning.value
}
const startSlotMachine = () => {
  firstSpeedOfRoll.value = initSpeedOfRoll + Math.floor(Math.random() * 10)
  secondSpeedOfRoll.value = initSpeedOfRoll + Math.floor(Math.random() * 10)
  thirdSpeedOfRoll.value = initSpeedOfRoll + Math.floor(Math.random() * 10)
  addSpanAndRoll(firstSection, firstActionTrigger, firstTimer, firstIterator, firstSpeedOfRoll)
  addSpanAndRoll(secondSection, secondActionTrigger, secondTimer, secondIterator, secondSpeedOfRoll)
  addSpanAndRoll(thirdSection, thirdActionTrigger, thirdTimer, thirdIterator, thirdSpeedOfRoll)
}
onMounted(() => {
  firstSpeedOfRoll.value = initSpeedOfRoll
  secondSpeedOfRoll.value = initSpeedOfRoll
  thirdSpeedOfRoll.value = initSpeedOfRoll
})
watch(
  () => firstActionTrigger.value,
  () => {
    stopRoll(firstTimer, firstSpeedOfRoll)
    removeSpanFromTheBottomOfSection(firstSection)
    if (isRunning.value) {
      addSpanAndRoll(firstSection, firstActionTrigger, firstTimer, firstIterator, firstSpeedOfRoll)
    }
  }
)
watch(
  () => secondActionTrigger.value,
  () => {
    stopRoll(secondTimer, secondSpeedOfRoll)
    removeSpanFromTheBottomOfSection(secondSection)
    if (isRunning.value) {
      addSpanAndRoll(
        secondSection,
        secondActionTrigger,
        secondTimer,
        secondIterator,
        secondSpeedOfRoll
      )
    }
  }
)
watch(
  () => thirdActionTrigger.value,
  () => {
    stopRoll(thirdTimer, thirdSpeedOfRoll)
    removeSpanFromTheBottomOfSection(thirdSection)
    if (isRunning.value) {
      addSpanAndRoll(thirdSection, thirdActionTrigger, thirdTimer, thirdIterator, thirdSpeedOfRoll)
    }
  }
)
</script>

<style scoped lang="scss">
main {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  background: rgb(223, 167, 167);

  .machine {
    width: 60%;
    height: 500px;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;

    div {
      width: calc(100% / 3);
      border: 1px solid #000;
      height: 100%;

      .roll {
        width: 100%;

        span {
          display: block;
          width: 100%;
          height: 100%;

          &:first-child {
            background: rgb(58, 218, 111, 0.5);
            margin-top: -500px;
          }

          &:last-child {
            background: rgb(58, 218, 111, 0.8);
          }
        }
      }
    }
  }

  button {
    margin-top: 20px;
    padding: 10px 20px;
    background: #000;
    color: #fff;
    border: none;
    cursor: pointer;
  }
}
</style>
