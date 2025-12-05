<script setup>
import { ref, onMounted } from 'vue'

const content = ref('')
const memos = ref([]) // 메모 리스트 담을 변수
const status = ref('대기 중...')


const API_URL = 'https://sdfeuxx3ch.execute-api.us-east-1.amazonaws.com/dev/memos'

// 1. 메모 목록 불러오기 (GET)
const fetchMemos = async () => {
  try {
    const res = await fetch(API_URL)
    if (!res.ok) throw new Error('불러오기 실패')

    const data = await res.json()
    memos.value = data // 받아온 데이터를 리스트에 넣기
    console.log('메모 로딩 완료:', data)
  } catch (e) {
    console.error(e)
    status.value = '목록 불러오기 실패...'
  }
}

// 2. 메모 저장하기 (POST)
const saveMemo = async () => {
  if (!content.value) return alert('내용을 입력하세요!')

  status.value = '저장 중...'

  try {
    const res = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ content: content.value })
    })

    if (!res.ok) throw new Error('저장 실패')

    status.value = '저장 성공!'
    content.value = ''
    fetchMemos() // 저장 후 목록 다시 불러오기 (새로고침 효과)

  } catch (e) {
    console.error(e)
    status.value = '저장 실패... (CORS?)'
  }
}

// 앱 켜지자마자 목록 불러오기
onMounted(() => {
  fetchMemos()
})
</script>

<template>
  <div style="padding: 40px; max-width: 600px; margin: 0 auto;">
    <h1>😈 Day 2: 메모장 완성</h1>

    <!-- 입력 폼 -->
    <div style="display: flex; gap: 10px; margin-bottom: 20px;">
      <input v-model="content" placeholder="오늘 할 일은?" style="flex: 1; padding: 10px;">
      <button @click="saveMemo" style="padding: 10px 20px;">저장</button>
    </div>
    <p>상태: {{ status }}</p>

    <hr style="margin: 30px 0;">

    <!-- 리스트 출력 -->
    <h2>📝 내 메모 목록 ({{ memos.length }}개)</h2>
    <ul style="list-style: none; padding: 0;">
      <li v-for="memo in memos" :key="memo.id"
          style="border: 1px solid #ddd; padding: 15px; margin-bottom: 10px; border-radius: 8px;">
        <div style="font-weight: bold; font-size: 1.1em;">{{ memo.content }}</div>
        <div style="color: #888; font-size: 0.8em; margin-top: 5px;">{{ memo.createdAt }}</div>
      </li>
    </ul>
  </div>
</template>