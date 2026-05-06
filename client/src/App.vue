<script setup>
import { computed, reactive } from 'vue'

const grades = reactive({
  avp1: '0',
  avp2: '0',
  tde1: '0',
  tde2: '0',
  tde3: '0',
  tde4: '0',
})

function sanitizeRaw(value) {
  if (value === '') return ''

  let next = String(value)
  if (next.length > 1 && next.startsWith('0') && next[1] !== '.') {
    next = next.replace(/^0+/, '') || '0'
  }

  const parsed = Number.parseFloat(next)
  if (Number.isNaN(parsed)) return '0'
  if (parsed > 10) return '10'
  if (parsed < 0) return '0'

  return String(parsed)
}

function numericValue(value) {
  if (value === '') return 0
  const parsed = Number.parseFloat(value)
  if (Number.isNaN(parsed)) return 0
  return Math.min(10, Math.max(0, parsed))
}

function onInput(key, event) {
  const sanitized = sanitizeRaw(event.target.value)
  grades[key] = sanitized
  if (event.target.value !== sanitized) {
    event.target.value = sanitized
  }
}

function onFocus(key) {
  if (grades[key] === '0') {
    grades[key] = ''
  }
}

function onBlur(key) {
  if (grades[key] === '') {
    grades[key] = '0'
  } else {
    grades[key] = sanitizeRaw(grades[key]) || '0'
  }
}

const tdes = computed(() => {
  const total = numericValue(grades.tde1) + numericValue(grades.tde2) + numericValue(grades.tde3) + numericValue(grades.tde4)
  return total / 4
})

const media = computed(() => ((2 * numericValue(grades.avp1)) + (2 * numericValue(grades.avp2)) + tdes.value) / 5)

const mediaClass = computed(() => {
  if (media.value >= 7) return 'text-emerald-600'
  if (media.value >= 5) return 'text-blue-600'
  return 'text-slate-700'
})
</script>

<template>
  <div class="min-h-screen bg-slate-50 text-slate-800 antialiased flex flex-col items-center px-3 py-4 sm:px-4 sm:py-8 md:py-10">
    <main class="w-full max-w-4xl bg-white border border-slate-200 rounded-xl sm:rounded-2xl shadow-sm p-3 sm:p-6 lg:p-10">
      <header class="mb-5 border-b border-slate-100 pb-4 sm:mb-8 sm:pb-6">
        <h1 class="text-xl leading-tight sm:text-2xl md:text-3xl font-semibold text-slate-900 tracking-tight mb-2">Avaliação de Desempenho</h1>
        <p class="text-xs sm:text-sm text-slate-500">Preencha suas notas abaixo para calcular a média final em tempo real.</p>
      </header>

      <div class="overflow-x-auto overscroll-x-contain pb-2 mb-6 sm:mb-8">
        <table class="w-full min-w-[640px] border-collapse text-left text-xs sm:text-sm md:text-base">
          <thead>
            <tr class="bg-slate-50/50">
              <th class="border-y border-slate-200 py-3 px-2 sm:px-4 font-semibold text-slate-600 text-[11px] sm:text-xs uppercase tracking-wider">Tipo de Avaliação</th>
              <th class="border-y border-slate-200 py-3 px-2 sm:px-4 font-semibold text-slate-600 text-[11px] sm:text-xs uppercase tracking-wider text-center">Pontuação</th>
              <th class="border-y border-slate-200 py-3 px-2 sm:px-4 font-semibold text-slate-600 text-[11px] sm:text-xs uppercase tracking-wider text-center">Peso</th>
              <th class="border-y border-slate-200 py-3 px-2 sm:px-4 font-semibold text-slate-600 text-[11px] sm:text-xs uppercase tracking-wider text-center">Nota Obtida</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100">
            <tr class="hover:bg-slate-50/50 transition-colors">
              <td class="py-3 sm:py-4 px-2 sm:px-4 font-medium text-slate-700">AVP1</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 text-center text-slate-500">0 a 10</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 text-center text-slate-500">2</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 flex justify-center">
                <input
                  :value="grades.avp1"
                  class="grade-input"
                  type="number"
                  min="0"
                  max="10"
                  step="0.1"
                  @input="onInput('avp1', $event)"
                  @focus="onFocus('avp1')"
                  @blur="onBlur('avp1')"
                />
              </td>
            </tr>
            <tr class="hover:bg-slate-50/50 transition-colors">
              <td class="py-3 sm:py-4 px-2 sm:px-4 font-medium text-slate-700">AVP2</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 text-center text-slate-500">0 a 10</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 text-center text-slate-500">2</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 flex justify-center">
                <input
                  :value="grades.avp2"
                  class="grade-input"
                  type="number"
                  min="0"
                  max="10"
                  step="0.1"
                  @input="onInput('avp2', $event)"
                  @focus="onFocus('avp2')"
                  @blur="onBlur('avp2')"
                />
              </td>
            </tr>
            <tr class="hover:bg-slate-50/50 transition-colors">
              <td class="py-3 sm:py-4 px-2 sm:px-4 font-medium text-slate-700">
                TDEs
                <span class="block text-xs text-slate-400 font-normal mt-0.5">Soma das 4 atividades</span>
              </td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 text-center text-slate-500">0 a 10</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4 text-center text-slate-500">1</td>
              <td class="py-3 sm:py-4 px-2 sm:px-4">
                <div class="flex flex-wrap items-center justify-center gap-1.5 sm:gap-2 max-w-[220px] mx-auto">
                  <input
                    :value="grades.tde1"
                    class="grade-input"
                    type="number"
                    min="0"
                    max="10"
                    step="0.1"
                    placeholder="TDE1"
                    title="TDE 1"
                    @input="onInput('tde1', $event)"
                    @focus="onFocus('tde1')"
                    @blur="onBlur('tde1')"
                  />
                  <input
                    :value="grades.tde2"
                    class="grade-input"
                    type="number"
                    min="0"
                    max="10"
                    step="0.1"
                    placeholder="TDE2"
                    title="TDE 2"
                    @input="onInput('tde2', $event)"
                    @focus="onFocus('tde2')"
                    @blur="onBlur('tde2')"
                  />
                  <input
                    :value="grades.tde3"
                    class="grade-input"
                    type="number"
                    min="0"
                    max="10"
                    step="0.1"
                    placeholder="TDE3"
                    title="TDE 3"
                    @input="onInput('tde3', $event)"
                    @focus="onFocus('tde3')"
                    @blur="onBlur('tde3')"
                  />
                  <input
                    :value="grades.tde4"
                    class="grade-input"
                    type="number"
                    min="0"
                    max="10"
                    step="0.1"
                    placeholder="TDE4"
                    title="TDE 4"
                    @input="onInput('tde4', $event)"
                    @focus="onFocus('tde4')"
                    @blur="onBlur('tde4')"
                  />
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="grid gap-4 sm:gap-6 md:grid-cols-2 items-start">
        <section class="text-xs sm:text-sm text-slate-500 space-y-2 bg-slate-50 p-3 sm:p-4 rounded-xl border border-slate-100">
          <h3 class="font-semibold text-slate-700 mb-3 text-xs uppercase tracking-wider">Fórmula de Cálculo</h3>
          <p><strong class="font-medium text-slate-700">Média</strong> = ((2 × AVP1) + (2 × AVP2) + TDEs) / 5</p>
          <p><strong class="font-medium text-slate-700">TDEs</strong> = (TDE1 + TDE2 + TDE3 + TDE4) / 4</p>
          <p class="pt-2 text-xs">As notas individuais são automaticamente limitadas ao intervalo de 0 a 10.</p>
        </section>

        <section class="bg-blue-50/50 border border-blue-100 rounded-xl p-4 sm:p-5 md:p-6">
          <div class="flex items-center justify-between gap-4 mb-3 sm:mb-4">
            <span class="text-slate-600 font-medium text-sm sm:text-base">Média dos TDEs</span>
            <span class="result-number text-base sm:text-lg font-semibold text-slate-700">{{ tdes.toFixed(2) }}</span>
          </div>
          <div class="flex items-center justify-between gap-4 border-t border-blue-200/60 pt-3 sm:pt-4">
            <span class="text-slate-900 font-semibold text-base sm:text-lg">Média Final</span>
            <span class="result-number text-xl sm:text-2xl font-bold" :class="mediaClass">{{ media.toFixed(2) }}</span>
          </div>
        </section>
      </div>
    </main>
  </div>
</template>

<style scoped>
.grade-input {
  width: 100%;
  min-width: 64px;
  max-width: 96px;
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  padding: 0.45rem 0.4rem;
  font-size: 0.95rem;
  background: #ffffff;
  color: #0f172a;
  text-align: center;
  font-variant-numeric: tabular-nums;
  transition: all 0.2s ease;
}

.grade-input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
}

.grade-input::-webkit-outer-spin-button,
.grade-input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.grade-input[type='number'] {
  -moz-appearance: textfield;
}

.result-number {
  font-variant-numeric: tabular-nums;
}

@media (min-width: 640px) {
  .grade-input {
    max-width: 90px;
    padding: 0.5rem;
  }
}
</style>
