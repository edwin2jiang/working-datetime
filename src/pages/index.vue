<script setup lang="ts">
const timeList = [
  {
    time: '9:00-10:00',
    action: '坐车 + 早饭',
  },
  {
    time: '10:00-12:00',
    action: '工作 + 早会',
  },
  {
    time: '12:00-14:00',
    action: '午饭 + 午休',
  },
  {
    time: '14:00-18:00',
    action: '工作',
  },
  {
    time: '19:00-20:00',
    action: '工作 + 准备日报',
  },
  {
    time: '20:00-22:00',
    action: '学习',
  },
  {
    time: '22:00-22:30',
    action: '回去',
  },
  {
    time: '22:30-23:00',
    action: '洗澡',
  },
  {
    time: '23:00-24:00',
    action: '休息',
  },
]

// 获取当前时间所在的时间段
function getCurrentTime() {
  const now = new Date()
  const hour = now.getHours()
  const minute = now.getMinutes()
  const time = `${hour}:${minute}`
  return time
}

// 获取当前时间所在的时间段
function getCurrentTimeIndex() {
  const time = getCurrentTime()

  const index = timeList.findIndex((item) => {
    const [start, end] = item.time.split('-')
    // 计算当前时间是否在时间段内
    // 小时:分钟 => 小时*60+分钟
    const [startHour, startMinute] = start.split(':')
    const [endHour, endMinute] = end.split(':')
    const startTime = Number.parseInt(startHour) * 60 + Number.parseInt(startMinute)
    const endTime = Number.parseInt(endHour) * 60 + Number.parseInt(endMinute)

    const [hour, minute] = time.split(':')
    const currentTime = Number.parseInt(hour) * 60 + Number.parseInt(minute)

    return currentTime >= startTime && currentTime <= endTime
  })

  return index
}

const currentIndex = getCurrentTimeIndex()
const currentIntervalTime = ref(getIntervalTime())

// 距离结束时间的时间间隔
function getIntervalTime() {
  const now = new Date()
  const hour = now.getHours()
  const minute = now.getMinutes()
  const [_, end] = timeList[currentIndex].time.split('-')
  const [endHour, endMinute] = end.split(':')
  const endTime = Number.parseInt(endHour) * 60 + Number.parseInt(endMinute)
  const currentTime = Number.parseInt(hour) * 60 + Number.parseInt(minute)
  return endTime - currentTime
}

setInterval(() => {
  const index = getCurrentTimeIndex()
  if (index !== currentIndex)
    currentIndex = index

  currentIntervalTime.value = getIntervalTime()
}, 1000)

function getNextIndex() {
  return currentIndex + 1 > timeList.length - 1 ? 0 : currentIndex + 1
}
</script>

<template>
  <div class="w-full">
    <div class="py-8 text-2xl text-gray-400">
      时间看板
    </div>
    <div class="h-70vh">
      <div class="grid grid-cols-2 gap-4">
        <!-- 时间list -->
        <div class="rounded-xl bg-gray-500 bg-opacity-20 p-4">
          <div class="text-center text-xl font-bold">
            时间
          </div>
          <div class="mt-4">
            <div v-for="item in timeList" :key="item.time" class="flex justify-between">
              <div>{{ item.time }}</div>
              <div>{{ item.action }}</div>
            </div>
          </div>
        </div>
        <div class="flex flex-col gap-4">
          <div class="rounded-xl bg-gray-500 bg-opacity-20 p-4">
            <h2 class="text-xl">
              当前任务
            </h2>
            <div class="mt-4 flex justify-center gap-4">
              <div>{{ timeList[currentIndex].time }}</div>
              <div>{{ timeList[currentIndex].action }}</div>
            </div>

            <h2 class="mt-4 text-lg text-red-500">
              还剩下{{ currentIntervalTime }}分钟
            </h2>
          </div>
          <div class="rounded-xl bg-gray-500 bg-opacity-20 p-4">
            <h2 class="text-xl">
              下一项任务
            </h2>
            <div class="mt-4 flex justify-center gap-4">
              <div>{{ timeList[getNextIndex()].time }}</div>
              <div>{{ timeList[getNextIndex()].action }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
