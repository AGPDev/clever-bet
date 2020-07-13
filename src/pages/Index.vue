<script>
const configInit = {
  balance: null,
  bet: null,
  chance: '49.5',
  multiply: '2',
  payout: '2'
}

export default {
  name: 'PageIndex',

  data () {
    return {}
  },

  created () {
    this.stake = Object.assign({}, configInit)
  }
}
</script>

<template lang="pug">
q-page(class="flex flex-center bg-blue-grey-14")
  q-card(class="q-pa-md bg-blue-grey-10")
    q-input(
      v-model="this.stake.balance"
      fill-mask="0"
      input-class="text-right"
      label="Balance"
      mask="#.########"
      reverse-fill-mask
      dark
      filled
    )

    q-input(
      v-model="this.stake.bet"
      fill-mask="0"
      input-class="text-right"
      label="Bet Initial"
      mask="#.########"
      reverse-fill-mask
      dark
      filled
    )
</template>
