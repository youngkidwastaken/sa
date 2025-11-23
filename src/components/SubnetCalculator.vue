<!-- src/components/SubnetCalculator.vue -->
<template>
  <div class="subnet-calculator">
    <h2>Калькулятор подсетей</h2>
    <form @submit.prevent="calculate" class="calculator-form">
      <div class="input-group">
        <label for="ip-input">IP-адрес:</label>
        <input
          id="ip-input"
          v-model="ipAddress"
          type="text"
          placeholder="192.168.1.150"
          class="ip-input"
        />
        <p v-if="!isIpValid(ipAddress) && ipAddress" class="error-message">
          Неверный IP-адрес
        </p>
      </div>

      <div class="input-group">
        <label for="mask-select">Маска подсети:</label>
        <select
          id="mask-select"
          v-model="selectedMask"
          class="mask-select"
        >
          <option
            v-for="option in subnetMaskOptions"
            :key="option.value"
            :value="option.value"
          >
            {{ option.label }} ({{ option.value }})
          </option>
        </select>
      </div>

      <button
        type="submit"
        :disabled="!isIpValid(ipAddress)"
        class="calculate-button"
      >
        Рассчитать
      </button>
    </form>

    <div v-if="results" class="results">
      <h3>Результаты:</h3>
      <p><strong>Введённый IP:</strong> {{ results.inputIp }}</p>
      <p><strong>Выбранная маска:</strong> {{ results.inputMask }}</p>
      <p><strong>Адрес сети:</strong> {{ results.networkAddress }}</p>
      <p><strong>Количество возможных адресов:</strong> {{ results.addressCount }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { SUBNET_MASK_OPTIONS } from '../constants/subnetMasks';
import { isIpValid, getNetworkAdress, getAddressesCount } from '../utils/ipUtils';

const ipAddress = ref<string>('');
const selectedMask = ref<string>(SUBNET_MASK_OPTIONS[24].value); // /24 по умолчанию

// Используем computed для опций селекта, если нужно (например, для сложных вычислений)
// const subnetMaskOptions = computed(() => SUBNET_MASK_OPTIONS);
const subnetMaskOptions = SUBNET_MASK_OPTIONS; // В данном случае просто используем константу

// Состояние для результатов
const results = ref<{
  inputIp: string;
  inputMask: string;
  networkAddress: string;
  addressCount: number;
} | null>(null);

const calculate = () => {
  if (isIpValid(ipAddress.value)) {
    const networkAddr = getNetworkAdress(ipAddress.value, selectedMask.value);
    const addrCount = getAddressesCount(selectedMask.value);

    results.value = {
      inputIp: ipAddress.value,
      inputMask: selectedMask.value,
      networkAddress: networkAddr,
      addressCount: addrCount,
    };
  }
};

// computed может быть полезен, если результат зависит от нескольких ref и не должен пересчитываться каждый раз
// В данном случае, расчет происходит только по событию, так что ref достаточно
</script>

<style scoped>
.subnet-calculator {
  max-width: 500px;
  margin: 2rem auto;
  padding: 1.5rem;
  border: 1px solid var(--border-color);
  border-radius: 8px;
  background-color: var(--bg-color);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

h2 {
  text-align: center;
  margin-bottom: 1.5rem;
  color: var(--text-color);
}

.calculator-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.input-group {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 0.5rem;
  font-weight: bold;
  color: var(--text-color);
}

.ip-input,
.mask-select {
  padding: 0.5rem;
  border: 1px solid var(--border-color);
  border-radius: 4px;
  font-size: 1rem;
  background-color: var(--input-bg-color);
  color: var(--input-text-color);
}

.error-message {
  color: #e74c3c; /* Красный цвет для ошибки */
  font-size: 0.875rem;
  margin-top: 0.25rem;
}

.calculate-button {
  padding: 0.75rem;
  background-color: var(--button-bg-color);
  color: var(--button-text-color);
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.calculate-button:hover:not(:disabled) {
  background-color: var(--button-hover-bg-color);
}

.calculate-button:disabled {
  background-color: #bdc3c7; /* Серый цвет, когда отключена */
  cursor: not-allowed;
}

.results {
  margin-top: 1.5rem;
  padding: 1rem;
  border: 1px solid var(--border-color);
  border-radius: 4px;
  background-color: var(--result-bg-color);
}

.results h3 {
  margin-top: 0;
  color: var(--text-color);
}

.results p {
  margin: 0.5rem 0;
  color: var(--text-color);
}

/* Определение переменных CSS */
:root {
  --bg-color: #f8f9fa; /* Светлый фон */
  --text-color: #2c3e50; /* Темный текст */
  --border-color: #dee2e6; /* Цвет границ */
  --input-bg-color: #ffffff; /* Белый фон инпутов */
  --input-text-color: #2c3e50; /* Цвет текста инпутов */
  --button-bg-color: #3498db; /* Синий фон кнопки */
  --button-text-color: #ffffff; /* Белый текст кнопки */
  --button-hover-bg-color: #2980b9; /* Темно-синий фон кнопки при наведении */
  --result-bg-color: #e8f4fd; /* Светло-голубой фон результатов */
}
</style>