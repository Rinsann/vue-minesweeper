<script setup lang="ts">

interface BlockState{
  x: number,
  y: number,
  revealed: boolean,
  mine?: boolean,
  flagged?: boolean,
  adjacentMines?: number
}

const WIDTH = 10
const HEIGHT = 10
const state = reactive(
  Array.from({ length: HEIGHT },(_,y) => 
    Array.from({ length: WIDTH },
    (_,x): BlockState => ({
      x,
      y,
      adjacentMines:0,
      revealed: false
    }) ,
    ),
  ),
)

function generateMines(){
  for(const row of state){
    for(const block of row)
      block.mine  = Math.random() < 0.3
  }
}

const directions = [
  [1,1],
  [1,0],
  [1,-1],
  [0,-1],
  [-1,-1],
  [-1,0],
  [-1,1],
  [0,1],
]

const numberColors = [
  'text-transparent',
  'text-blue-500',
  'text-green-500',
  'text-yellow-500',
  'text-orange-500',
  'text-red-500',
  'text-purple-500',
  'text-pink-500',
  'text-teal-500',
]

function updateNumbers () {
  state.forEach((raw,y)=>{
    raw.forEach((block,x)=>{
      if(block.mine)
        return
      directions.forEach(([dx,dy])=>{
        const x2 = x + dx
        const y2 = y + dy
        if(x2 < 0 || x2 >= WIDTH || y2 < 0 || y2 >= HEIGHT)
          return
        
        if(state[y2][x2].mine)
          block.adjacentMines += 1
      })
    })
  })
}

function onClick(x:number,y:number){
  console.log(`Clicked at ${x},${y}`);
}

function getBlockClass(block:BlockState){
  return block.mine ? 
  'bg-red-500/50' 
  : numberColors[block.adjacentMines]
}

generateMines()
updateNumbers()
</script>

<template>
  <div>
    Minesweeper

    <div p5>

    <div 
      v-for="row,y in state" 
      :key="y"
      flex="~"
      items-center justify-center
      >

    <button 
    v-for="item,x in row" :key="x"
      flex="~"
      items-center justify-center
      w-10 h-10 m="0.5"
      hover="bg-gray/10"
      border="1 gray-400/10"
      :class="getBlockClass(item)"
      @click="onClick(x,y)"
    >
      <div v-if="item.mine" i-mdi:mine>
        x
      </div>
      <div v-else>
        {{ item.adjacentMines }}
      </div>
    </button>
      
    </div>
  </div>

  </div>
</template>
