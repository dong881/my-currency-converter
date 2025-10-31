<template>
  <div class="currency-converter">
    <h1>貨幣轉換器</h1>
    <div class="converter-container">
      <div class="input-group">
        <label for="amount">金額</label>
        <input
          id="amount"
          v-model.number="amount"
          type="number"
          placeholder="輸入金額"
          min="0"
          step="0.01"
        />
      </div>

      <div class="currency-select-group">
        <div class="select-wrapper">
          <label for="fromCurrency">來源貨幣</label>
          <select id="fromCurrency" v-model="fromCurrency">
            <option v-for="currency in currencies" :key="currency.code" :value="currency.code">
              {{ currency.code }} - {{ currency.name }}
            </option>
          </select>
        </div>

        <button class="swap-button" @click="swapCurrencies" title="交換貨幣">
          ⇄
        </button>

        <div class="select-wrapper">
          <label for="toCurrency">目標貨幣</label>
          <select id="toCurrency" v-model="toCurrency">
            <option v-for="currency in currencies" :key="currency.code" :value="currency.code">
              {{ currency.code }} - {{ currency.name }}
            </option>
          </select>
        </div>
      </div>

      <button class="convert-button" @click="convertCurrency" :disabled="loading">
        {{ loading ? '轉換中...' : '轉換' }}
      </button>

      <div v-if="error" class="error-message">
        {{ error }}
      </div>

      <div v-if="result !== null" class="result">
        <h2>轉換結果</h2>
        <p class="result-amount">
          {{ amount.toFixed(2) }} {{ fromCurrency }} =
          <strong>{{ result.toFixed(2) }} {{ toCurrency }}</strong>
        </p>
        <p class="exchange-rate">
          匯率: 1 {{ fromCurrency }} = {{ exchangeRate.toFixed(6) }} {{ toCurrency }}
        </p>
        <p v-if="lastUpdate" class="last-update">
          最後更新: {{ lastUpdate }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CurrencyConverter',
  data() {
    return {
      amount: 1,
      fromCurrency: 'USD',
      toCurrency: 'TWD',
      result: null,
      exchangeRate: 0,
      loading: false,
      error: null,
      lastUpdate: null,
      currencies: [
        { code: 'USD', name: '美元' },
        { code: 'EUR', name: '歐元' },
        { code: 'GBP', name: '英鎊' },
        { code: 'JPY', name: '日圓' },
        { code: 'TWD', name: '新台幣' },
        { code: 'CNY', name: '人民幣' },
        { code: 'HKD', name: '港幣' },
        { code: 'KRW', name: '韓元' },
        { code: 'SGD', name: '新加坡幣' },
        { code: 'AUD', name: '澳幣' },
        { code: 'CAD', name: '加幣' },
        { code: 'CHF', name: '瑞士法郎' },
        { code: 'NZD', name: '紐西蘭幣' },
        { code: 'THB', name: '泰銖' },
        { code: 'MYR', name: '馬來西亞幣' },
        { code: 'PHP', name: '菲律賓披索' },
        { code: 'IDR', name: '印尼盾' },
        { code: 'VND', name: '越南盾' },
        { code: 'INR', name: '印度盧比' },
        { code: 'BRL', name: '巴西雷亞爾' },
        { code: 'MXN', name: '墨西哥披索' },
        { code: 'ZAR', name: '南非蘭特' },
        { code: 'SEK', name: '瑞典克朗' },
        { code: 'NOK', name: '挪威克朗' },
        { code: 'DKK', name: '丹麥克朗' },
        { code: 'PLN', name: '波蘭茲羅提' },
        { code: 'TRY', name: '土耳其里拉' },
        { code: 'RUB', name: '俄羅斯盧布' },
      ]
    }
  },
  methods: {
    async convertCurrency() {
      if (this.amount === null || this.amount === undefined || this.amount <= 0) {
        this.error = '請輸入有效的金額'
        return
      }

      this.loading = true
      this.error = null
      this.result = null

      try {
        // Using ExchangeRate-API (free, no API key required)
        const response = await fetch(
          `https://api.exchangerate-api.com/v4/latest/${this.fromCurrency}`
        )

        if (!response.ok) {
          throw new Error('無法獲取匯率資料')
        }

        const data = await response.json()
        
        if (data.rates && data.rates[this.toCurrency]) {
          this.exchangeRate = data.rates[this.toCurrency]
          this.result = this.amount * this.exchangeRate
          // ExchangeRate-API uses time_last_updated (seconds since epoch)
          if (data.time_last_updated) {
            this.lastUpdate = new Date(data.time_last_updated * 1000).toLocaleString('zh-TW')
          }
        } else {
          throw new Error('無法找到指定的貨幣匯率')
        }
      } catch (err) {
        this.error = err.message || '轉換失敗，請稍後再試'
        console.error('Currency conversion error:', err)
      } finally {
        this.loading = false
      }
    },
    swapCurrencies() {
      const temp = this.fromCurrency
      this.fromCurrency = this.toCurrency
      this.toCurrency = temp
      
      if (this.result !== null) {
        this.convertCurrency()
      }
    }
  },
  mounted() {
    // Auto-convert on mount
    this.convertCurrency()
  }
}
</script>

<style scoped>
.currency-converter {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  color: #2c3e50;
  text-align: center;
  margin-bottom: 30px;
}

.converter-container {
  background: #fff;
  border-radius: 10px;
  padding: 30px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.input-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 8px;
  color: #2c3e50;
  font-weight: 600;
}

input[type="number"] {
  width: 100%;
  padding: 12px;
  font-size: 16px;
  border: 2px solid #e0e0e0;
  border-radius: 5px;
  box-sizing: border-box;
  transition: border-color 0.3s;
}

input[type="number"]:focus {
  outline: none;
  border-color: #42b983;
}

.currency-select-group {
  display: flex;
  align-items: flex-end;
  gap: 10px;
  margin-bottom: 20px;
}

.select-wrapper {
  flex: 1;
}

select {
  width: 100%;
  padding: 12px;
  font-size: 16px;
  border: 2px solid #e0e0e0;
  border-radius: 5px;
  background: white;
  cursor: pointer;
  transition: border-color 0.3s;
}

select:focus {
  outline: none;
  border-color: #42b983;
}

.swap-button {
  padding: 12px 16px;
  font-size: 20px;
  background: #f0f0f0;
  border: 2px solid #e0e0e0;
  border-radius: 5px;
  cursor: pointer;
  transition: all 0.3s;
  margin-bottom: 0;
}

.swap-button:hover {
  background: #42b983;
  color: white;
  border-color: #42b983;
  transform: rotate(180deg);
}

.convert-button {
  width: 100%;
  padding: 15px;
  font-size: 18px;
  font-weight: 600;
  color: white;
  background: #42b983;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
}

.convert-button:hover:not(:disabled) {
  background: #35a372;
}

.convert-button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.error-message {
  margin-top: 20px;
  padding: 15px;
  background: #fee;
  color: #c33;
  border: 1px solid #fcc;
  border-radius: 5px;
  text-align: center;
}

.result {
  margin-top: 30px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 5px;
  text-align: center;
}

.result h2 {
  color: #2c3e50;
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 20px;
}

.result-amount {
  font-size: 24px;
  color: #2c3e50;
  margin: 15px 0;
}

.result-amount strong {
  color: #42b983;
  font-size: 28px;
}

.exchange-rate {
  color: #666;
  margin: 10px 0;
}

.last-update {
  color: #999;
  font-size: 14px;
  margin: 10px 0 0 0;
}
</style>
