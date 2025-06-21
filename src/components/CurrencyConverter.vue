<template>
  <q-card class="q-pa-xl q-mx-auto" style="max-width: 500px; min-width: 350px;">
    <div class="text-h5 text-center text-primary q-mb-md flex flex-center items-center">
      <q-icon name="trending_up" color="green" size="32px" class="q-mr-sm" />
      Conversión de Monedas
    </div>
    <q-form @submit.prevent="convertCurrency">
      <q-input
        v-model.number="amount"
        label="Monto a convertir"
        type="number"
        :rules="[val => val > 0 || 'Ingrese un monto válido']"
        outlined
        class="q-mb-md"
      />
      <div class="row q-col-gutter-md q-mb-md">
        <div class="col">
          <div class="text-caption q-mb-xs">Desde</div>
          <q-select
            v-model="from"
            :options="currencyOptions"
            label=""
            option-label="label"
            option-value="value"
            emit-value
            map-options
            outlined
            style="min-width: 150px;"
          />
        </div>
        <div class="col-auto flex flex-center">
          <q-btn icon="swap_horiz" flat round @click="swapCurrencies" :disable="!from || !to" size="lg" />
        </div>
        <div class="col">
          <div class="text-caption q-mb-xs">Hacia</div>
          <q-select
            v-model="to"
            :options="currencyOptions"
            label=""
            option-label="label"
            option-value="value"
            emit-value
            map-options
            outlined
            style="min-width: 150px;"
          />
        </div>
      </div>
      <div class="q-mt-md">
        <q-btn label="Convertir" color="primary" type="submit" class="full-width" :disable="loading" style="font-size: 1.1em; height: 44px; background: linear-gradient(90deg, #3b82f6 0%, #6366f1 100%);" />
      </div>
    </q-form>
    <q-banner v-if="error" class="q-mt-md bg-red-2 text-red-10" style="font-size: 1.1em;">{{ error }}</q-banner>
    <q-banner v-if="result" class="q-mt-md bg-green-2 text-green-10" style="font-size: 1.1em;">{{ result }}</q-banner>
  </q-card>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const amount = ref(1)
const from = ref(null)
const to = ref(null)
const currencyOptions = ref([])
const result = ref('')
const error = ref('')
const loading = ref(false)

onMounted(async () => {
  try {
    const res = await fetch('https://api.frankfurter.app/currencies')
    const data = await res.json()
    currencyOptions.value = Object.entries(data).map(([value, label]) => ({ value, label }))
  } catch {
    error.value = 'No se pudieron cargar las monedas.'
  }
})

function swapCurrencies() {
  const temp = from.value
  from.value = to.value
  to.value = temp
}

async function convertCurrency() {
  error.value = ''
  result.value = ''
  if (!amount.value || amount.value <= 0) {
    error.value = 'Ingrese un monto válido.'
    return
  }
  if (!from.value || !to.value) {
    error.value = 'Seleccione ambas monedas.'
    return
  }
  loading.value = true
  try {
    const url = `https://api.frankfurter.app/latest?amount=${amount.value}&from=${from.value}&to=${to.value}`
    const res = await fetch(url)
    const data = await res.json()
    if (data.rates && data.rates[to.value]) {
      result.value = `${amount.value} ${from.value} equivalen a ${data.rates[to.value].toFixed(2)} ${to.value}`
    } else {
      error.value = 'No se pudo realizar la conversión.'
    }
  } catch {
    error.value = 'Error al conectar con el servicio.'
  } finally {
    loading.value = false
  }
}
</script>
