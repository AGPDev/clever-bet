<script>
const configInit = {
  balance: '0.00000000',
  bet: '0.00000000',
  chance: '49.5',
  multiply: '100',
  payout: '2'
}

export default {
  name: 'PageIndex',

  data () {
    return {
      bets: [],
      losses: null,
      persistence: null,
      stake: Object.assign({}, configInit),
      columns: [
        { name: 'id', align: 'center', label: '#', field: 'id' },
        { name: 'amount', align: 'center', label: 'Bet Amount', field: 'amount' },
        { name: 'afterLoss', align: 'center', label: 'After Loss', field: 'afterLoss' },
        { name: 'streakOdds', align: 'center', label: 'Losing Streak Odds', field: 'streakOdds' }
        // { name: 'profit', align: 'center', label: 'Profit If Win', field: 'profit' },
      ],
      pagination: {
        rowsPerPage: 0
      }
    }
  },

  methods: {
    submit () {
      const bets = []
      const multiply = 1 + parseFloat(this.stake.multiply) / 100
      const payout = parseFloat(this.stake.payout)
      const bodds = (1 - 1 / payout * 0.99)
      let currentBet = parseFloat(this.stake.bet)
      let currentBalance = parseFloat(this.stake.balance)
      let currentStreakOdds = bodds

      for (let id = 1; id < 1501; id++) {
        if (currentBet > (currentBalance + currentBet)) break

        currentBalance -= currentBet
        bets.push({
          id,
          amount: currentBet.toFixed(8),
          afterLoss: currentBalance.toFixed(8),
          streakOdds: (currentStreakOdds * 100).toFixed(8) + '%'
        })

        currentStreakOdds *= bodds
        currentBet *= multiply
      }

      this.losses = bets.length - 1
      this.persistence = (this.losses / payout).toFixed(2)
      this.bets = bets
    },

    setPayout (chance) {
      this.stake.payout = (99 / parseFloat(chance)).toFixed(4)
      this.stake.multiply = (100 / (this.stake.payout - 1)).toFixed(2)
    }
  }
}
</script>

<template lang="pug">
q-page(class="q-gutter-md bg-blue-grey-14")
  .row.justify-center.q-pt-md
    .col-auto
      q-card(class="q-py-lg bg-blue-grey-10")
        .row.q-gutter-md.justify-center
          q-input(
            v-model="stake.balance"
            fill-mask="0"
            input-class="text-right"
            label="Balance"
            mask="#.########"
            bottom-slots
            dark
            filled
            no-error-icon
            reverse-fill-mask
          )

          q-input(
            v-model="stake.bet"
            fill-mask="0"
            input-class="text-right"
            label="Bet Initial"
            mask="#.########"
            bottom-slots
            dark
            filled
            no-error-icon
            reverse-fill-mask
          )

          q-input(
            v-model="stake.payout"
            input-class="text-right"
            label="Payout"
            bottom-slots
            dark
            filled
            no-error-icon
          )

          q-input(
            @input="setPayout"
            v-model="stake.chance"
            input-class="text-right"
            label="Chance"
            suffix="%"
            bottom-slots
            dark
            filled
            no-error-icon
          )

          q-input(
            v-model="stake.multiply"
            input-class="text-right"
            label="Multiply on Loss"
            suffix="%"
            bottom-slots
            dark
            filled
            no-error-icon
          )

          .col-12.text-center
            q-btn(
              @click="submit"
              label="Calculate"
              color="deep-purple"
              no-caps
            )

  .row.justify-center
    .col-auto(v-if="losses && persistence")
      q-card(class="q-pa-lg bg-blue-grey-10")
        .row.q-gutter-md.justify-center.text-white
          .col-12
            | Your strategy supports <span class="text-weight-bold text-red">{{ losses }}</span> losses
          .col-12
            | The persistence of this strategy is <span class="text-weight-bold text-red">{{ persistence }}</span> (Minimum 8 Recommended)

  .row.justify-center
    .col-12.col-sm-8.col-md-6.col-lg-6.col-xl-5
      q-table(
        :data="bets"
        :columns="columns"
        :pagination="pagination"
        card-class="bg-blue-grey-10"
        title="Your Bets"
        dense
        dark
        hide-pagination
      )
</template>
