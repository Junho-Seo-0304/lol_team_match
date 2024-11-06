<template>
  <h1>해용이 출산 배 근바람 팀 나누기</h1>
  <div class="player-container">
    <h2>고수</h2>
    <ul>
      <li v-for="player in high_level" :key="player">
        {{ player }}
        <button class="remove-button" @click="removeHighLevel(player)">
          X
        </button>
      </li>
    </ul>
    <input type="text" v-model="high_level_input" @keyup.enter="addHighLevel" />
    <button @click="addHighLevel">추가</button>
  </div>
  <div class="player-container">
    <h2>중수</h2>
    <ul>
      <li v-for="player in middle_level" :key="player">
        {{ player }}
        <button class="remove-button" @click="removeMiddleLevel(player)">
          X
        </button>
      </li>
    </ul>
    <input
      type="text"
      v-model="middle_level_input"
      @keyup.enter="addMiddleLevel"
    />
    <button @click="addMiddleLevel">추가</button>
  </div>
  <div class="player-container">
    <h2>초보</h2>
    <ul>
      <li v-for="player in low_level" :key="player">
        {{ player }}
        <button class="remove-button" @click="removeLowLevel(player)">X</button>
      </li>
    </ul>
    <input type="text" v-model="low_level_input" @keyup.enter="addLowLevel" />
    <button @click="addLowLevel">추가</button>
  </div>
  <button @click="divideTeam">팀 나누기</button>
  <div class="team-container">
    <div class="team-result-container">
      <h2>팀 1</h2>
      <ul>
        <li v-for="player in team1" :key="player">{{ player }}</li>
      </ul>
    </div>
    <div class="team-result-container">
      <h2>팀 2</h2>
      <ul>
        <li v-for="player in team2" :key="player">{{ player }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const high_level_input = ref("");
const high_level = ref([]);
const middle_level_input = ref("");
const middle_level = ref([]);
const low_level_input = ref("");
const low_level = ref([]);
const team1 = ref([]);
const team2 = ref([]);

const addHighLevel = () => {
  high_level.value.push(high_level_input.value);
  high_level_input.value = "";
};
const addMiddleLevel = () => {
  middle_level.value.push(middle_level_input.value);
  middle_level_input.value = "";
};
const addLowLevel = () => {
  low_level.value.push(low_level_input.value);
  low_level_input.value = "";
};
const removeHighLevel = (player) => {
  high_level.value = high_level.value.filter((p) => p !== player);
};
const removeMiddleLevel = (player) => {
  middle_level.value = middle_level.value.filter((p) => p !== player);
};
const removeLowLevel = (player) => {
  low_level.value = low_level.value.filter((p) => p !== player);
};
const divideTeam = async () => {
  // 총 인원이 10명인지 확인
  const totalPlayers =
    high_level.value.length +
    middle_level.value.length +
    low_level.value.length;
  if (totalPlayers !== 10) {
    alert("총 인원이 10명이어야 합니다.");
    return;
  }

  // 기존 팀 초기화
  team1.value = [];
  team2.value = [];

  // 각 레벨별 선수들을 복사
  const highLevelPlayers = [...high_level.value];
  const middleLevelPlayers = [...middle_level.value];
  const lowLevelPlayers = [...low_level.value];

  // 상급자 분배
  while (highLevelPlayers.length > 0) {
    if (team1.value.length <= team2.value.length) {
      team1.value.push(
        highLevelPlayers.splice(
          Math.floor(Math.random() * highLevelPlayers.length),
          1
        )[0]
      );
    } else {
      team2.value.push(
        highLevelPlayers.splice(
          Math.floor(Math.random() * highLevelPlayers.length),
          1
        )[0]
      );
    }
  }

  // 상급자 수 차이 확인
  const team1HighCount = team1.value.length;
  const team2HighCount = team2.value.length;

  // 초급자 분배 - 상급자가 많은 팀에 우선 배정
  while (lowLevelPlayers.length > 0) {
    if (team1HighCount > team2HighCount) {
      team1.value.push(
        lowLevelPlayers.splice(
          Math.floor(Math.random() * lowLevelPlayers.length),
          1
        )[0]
      );
    } else if (team2HighCount > team1HighCount) {
      team2.value.push(
        lowLevelPlayers.splice(
          Math.floor(Math.random() * lowLevelPlayers.length),
          1
        )[0]
      );
    } else {
      // 상급자 수가 같으면 균등하게 배분
      if (team1.value.length <= team2.value.length) {
        team1.value.push(
          lowLevelPlayers.splice(
            Math.floor(Math.random() * lowLevelPlayers.length),
            1
          )[0]
        );
      } else {
        team2.value.push(
          lowLevelPlayers.splice(
            Math.floor(Math.random() * lowLevelPlayers.length),
            1
          )[0]
        );
      }
    }
  }

  // 중급자로 나머지 자리 채우기
  while (middleLevelPlayers.length > 0) {
    if (team1.value.length < 5) {
      team1.value.push(
        middleLevelPlayers.splice(
          Math.floor(Math.random() * middleLevelPlayers.length),
          1
        )[0]
      );
    } else {
      team2.value.push(
        middleLevelPlayers.splice(
          Math.floor(Math.random() * middleLevelPlayers.length),
          1
        )[0]
      );
    }
  }

  // 팀 배정 결과를 하나씩 보여주기
  const tempTeam1 = [...team1.value];
  const tempTeam2 = [...team2.value];
  team1.value = [];
  team2.value = [];

  for (let i = 0; i < 5; i++) {
    await new Promise((resolve) => setTimeout(resolve, 1000));
    team1.value.push(tempTeam1[i]);
    await new Promise((resolve) => setTimeout(resolve, 1000));
    team2.value.push(tempTeam2[i]);
  }
};
</script>

<style scoped>
.team-container {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.remove-button {
  background-color: red;
  color: white;
}

/* 추가되는 CSS */
h1 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 2rem;
}

h2 {
  color: #42b883;
  margin-bottom: 1rem;
}

div {
  margin-bottom: 2rem;
}

.player-container ul {
  list-style: none;
  padding: 0;
  margin-bottom: 1rem;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}

.player-container li {
  padding: 0.5rem;
  background-color: #f8f9fa;
  margin-bottom: 0; /* margin-bottom 제거 */
  border-radius: 4px;
  text-align: center;
}

.team-result-container ul {
  list-style: none;
  padding: 0;
  margin-bottom: 1rem;
}

.team-result-container li {
  padding: 0.5rem;
  background-color: #f8f9fa;
  margin-bottom: 0;
  border-radius: 4px;
}

input {
  padding: 0.5rem;
  margin-right: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button {
  padding: 0.5rem 1rem;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #3aa876;
}

button[type="divideTeam"] {
  display: block;
  margin: 2rem auto;
  font-size: 1.1rem;
  padding: 0.75rem 1.5rem;
}

.team-container > div {
  background-color: #f8f9fa;
  padding: 1.5rem;
  border-radius: 8px;
  min-width: 200px;
}
</style>
