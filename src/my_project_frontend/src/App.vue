<script setup>
import { ref } from 'vue';
import { my_project_backend } from 'declarations/my_project_backend/index';
import { conditionalDelay } from '@dfinity/agent/lib/cjs/polling/strategy';
let rates = ref('');
const iloscWaluty = ref(0)

const getDataFromNBP = async () => {
  const res = await fetch("https://api.nbp.pl/api/exchangerates/tables/A/?format=json")
  const jsonData = await res.json();
  console.log(jsonData)
  rates.value = jsonData[0].rates
  console.log(rates)
}

const kupWalute = async (index) => {
  console.log(rates.value[index])
  const ilosc = BigInt(iloscWaluty.value)
  const cena = BigInt(rates.value[index].mid * 10e16)
  const koszt = await my_project_backend.calculate_currency_price(ilosc, cena)
  console.log(koszt/BigInt(10e16))

  my_project_backend.calculate_currency_price()

}

getDataFromNBP()
</script>

<template>
  <main>
    <img src="/logo2.svg" alt="DFINITY logo" />
    <br />
    <br />
    <table>
    <tr>
      <th>Nazwa waluty</th>
      <th>Kod waluty</th>
      <th>Cena</th>
      <th>Ilość waluty do zakupu</th>
      <th></th>
    </tr>
    <tr v-for="(rate, index) in rates">
      <td>{{ rate.currency }}</td>
      <td>{{ rate.code }}</td>
      <td>{{ rate.mid }}</td>
      <td><input type="number" v-model="iloscWaluty" /></td>
      <td><button @click="kupWalute(index)">KUP</button></td>
    </tr>
  </table>
  </main>
</template>
