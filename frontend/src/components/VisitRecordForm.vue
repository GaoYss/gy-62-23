<template>
  <form class="form-grid" @submit.prevent="handleSubmit">
    <label>
      预约
      <select v-model.number="model.appointment_id" required>
        <option disabled value="">请选择</option>
        <option v-for="item in appointments" :key="item.id" :value="item.id">
          {{ item.resident.name }} · {{ item.family_name }} · {{ formatTime(item.visit_time) }}
        </option>
      </select>
    </label>
    <label>
      签到时间
      <input v-model="model.check_in_time" type="datetime-local" required />
    </label>
    <label>
      签退时间
      <input v-model="model.check_out_time" type="datetime-local" />
    </label>
    <label>
      体温
      <input v-model="model.visitor_temperature" type="number" step="0.1" min="30" max="45" />
    </label>
    <label>
      接待员工
      <input v-model="model.staff_name" required />
    </label>
    <label class="span-2">
      探视记录
      <textarea v-model="model.summary" rows="3"></textarea>
    </label>
    <button class="primary" type="submit">登记探视记录</button>
  </form>
</template>

<script setup>
const TEMP_HARD_MIN = 30
const TEMP_HARD_MAX = 45
const TEMP_WARN_MIN = 35
const TEMP_WARN_MAX = 39

const props = defineProps({
  model: { type: Object, required: true },
  appointments: { type: Array, default: () => [] }
})
const emit = defineEmits(['submit'])

function formatTime(value) {
  return value ? new Date(value).toLocaleString('zh-CN') : ''
}

function handleSubmit() {
  const temp = parseFloat(props.model.visitor_temperature)
  if (!isNaN(temp)) {
    if (temp < TEMP_HARD_MIN || temp > TEMP_HARD_MAX) {
      alert(`体温 ${temp}°C 超出合理范围（${TEMP_HARD_MIN}~${TEMP_HARD_MAX}°C），请检查输入`)
      return
    }
    if (temp < TEMP_WARN_MIN || temp > TEMP_WARN_MAX) {
      const reason = temp < TEMP_WARN_MIN ? '偏低' : '偏高'
      if (!confirm(`体温 ${temp}°C ${reason}（正常范围 ${TEMP_WARN_MIN}~${TEMP_WARN_MAX}°C），确认提交吗？`)) {
        return
      }
    }
  }
  emit('submit')
}
</script>
