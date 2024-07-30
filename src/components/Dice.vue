<script setup lang="ts">
import { ref } from 'vue'
import { nanoid } from 'nanoid'
import ConfettiExplosion from "vue-confetti-explosion";

const NumberArr = [1, 2, 3, 4, 5, 5,6, 1, 2, 3]
const AllNewDice = () => NumberArr.map(() => GenerateDice())
const NewDice = ref(AllNewDice())
const Tenzies = ref(false)

  // Checking if player has Tenzies
  function HasTenzies () {
  const AllClicked = NewDice.value.every(Dice => Dice.IsClicked)
  const Value = NewDice.value[0].value
  const CheckValues = NewDice.value.every(Dice => Dice.value === Value)
  if (AllClicked && CheckValues) {
    Tenzies.value  = true
  }
}

const PlayNewGame = () => {
  Tenzies.value= false
  NewDice.value = AllNewDice()
}

function GenerateDice() {
  return {
    value: Math.floor(Math.random() * 6 + 1 ) ,
    IsClicked: false,
    id: nanoid()
  }
}

function HoldDice(Id) {
  NewDice.value = NewDice.value.map(item => {
    return item.id === Id ? { ...item, IsClicked: !item.IsClicked } : item
  })
  HasTenzies()

}

function RollDice() {
  NewDice.value = NewDice.value.map(dice => {
   return !dice.IsClicked ? GenerateDice() : dice
  })
}

</script>

<template>
      <h1>Tenzies</h1>
      <h2>Roll until all dice are the same. Click each die to freeze it at its current value between rolls</h2>
    <section class="DisplayGrid">
      <div class="Dice"
           :class="{Clicked : dice.IsClicked}"
           @click="() => HoldDice(dice.id)"
           v-for="dice in NewDice" :key="dice.id"> {{ dice.value }}
        <confetti-explosion v-show="Tenzies"/>
      </div>
    </section>
    <button
        @click="Tenzies ? PlayNewGame() : RollDice()"> {{Tenzies ? "New Game" : "Roll"}}</button>
</template>

<style scoped>
  h1 ,h2 {
    font-family: Chalkboard;
  }
  h1 {
    text-align: center;
  }
  h2 {
    max-width: 70%;
    margin: 0 auto;
    font-size: 1rem;
  }

</style>
