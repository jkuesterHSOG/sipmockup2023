<template>
  <el-container>
    <el-aside class="scrollbar-aside" width="200px">
      <el-scrollbar style="height: 600px;">
        <el-tag v-show="!hasCurrentUser()" v-for="(item, index) in users" :key="index" class="scrollbar-demo-item"
          @click.prevent="selectUser(index)">{{ item.name }}</el-tag>
      </el-scrollbar>
    </el-aside>
    <el-container>
      <el-card class="box-card">
        <el-header style="height: 100px; width: 500px">
          <el-tag size="large" style="width: 100px" v-show="hasCurrentUser()">{{ users[currentUser]?.name }}</el-tag>
          <el-button type="danger" plain style="margin-left: 50px;" v-show="hasCurrentUser()"
            @click.prevent="resetUser()">Anderer
            Benutzer</el-button>
          <el-divider />
          <div>
            <el-button type="primary" :disabled="!hasCurrentUser()" @click.prevent="clickCoffee()">Kaffee</el-button>
            <el-button type="primary" :disabled="!hasCurrentUser()" @click.prevent="clickMoney()">Einzahlung</el-button>
          </div>
          <p v-show="hasCurrentUser()"> Aktueller Saldo: {{ users[currentUser]?.amount }} EUR. </p>
        </el-header>
        <el-divider />
        <el-main>
          <div v-show="hasCurrentUser() && currentContext === 'coffee'">{{ numberOfCoffees }}x Kaffee gebucht</div>
          <div v-show="hasCurrentUser() && currentContext === 'money'">
            Saldo ausgleichen:
            <div>
              <el-button type="primary" @click.prevent="payUp(1.50)">1,50 EUR</el-button>
              <el-button type="primary" @click.prevent="payUp(5.00)">5,00 EUR</el-button>
              <el-button type="primary" @click.prevent="payUp(10.00)">10,00 EUR</el-button>
              <el-button v-show="users[currentUser]?.amount < 0" type="primary"
                @click.prevent="payUpFull()">{{ parseFloat(users[currentUser]?.amount
                  * -1).toFixed(2) }} EUR</el-button>
            </div>
          </div>
        </el-main>
      </el-card>
    </el-container>
  </el-container>
</template>

<script>
import { reactive, toRefs, onBeforeMount } from 'vue'

export default {
  setup() {
    const state = reactive({
      users: [],
      currentContext: '',
      numberOfCoffees: 0,
      timeRemaining: 0,
      currentUser: -1,
    })

    const populateUsers = () => {
      const arr = [];
      for (let i = 0; i < 20; i++) {
        const randomAmount = parseFloat((Math.random() * 100).toFixed(2));
        arr.push({ name: `Name ${i}`, amount: -randomAmount });
      }
      return arr;
    };


    const hasCurrentUser = () => {
      return (state.currentUser === -1) ? false : true;
    };

    const selectUser = (index) => {
      state.currentUser = index;
      state.currentAmount = state.users[index].amount;
    };

    const clickCoffee = () => {
      state.currentContext = 'coffee';
      state.numberOfCoffees++;
      const priorAmount = parseFloat(state.users[state.currentUser].amount);
      state.users[state.currentUser].amount = parseFloat(priorAmount - 1.50).toFixed(2);

    };

    const clickMoney = () => {
      state.currentContext = 'money';

    };

    const payUp = (credit) => {
      const priorAmount = parseFloat(state.users[state.currentUser].amount);
      state.users[state.currentUser].amount = parseFloat(priorAmount + credit).toFixed(2);
    };

    const payUpFull = () => {
      state.users[state.currentUser].amount = 0.00; 
    };


    const startTimer = () => {
      state.timeRemaining = 20;
      let timerInterval = setInterval(() => {
        state.timeRemaining--
        if (state.timeRemaining <= 0) {
          clearInterval(state.timerInterval);
          resetUser();
        }
      }, 1000)
    };

    const resetUser = () => {
      state.currentUser = -1;
      state.currentAmount = 0;
      state.currentContext = '';
      state.numberOfCoffees = 0;
    }
    onBeforeMount(() => {
      state.users = populateUsers();
    });

    return {
      payUp,
      payUpFull,
      clickCoffee,
      clickMoney,
      startTimer,
      hasCurrentUser,
      resetUser,
      selectUser,
      ...toRefs(state),
    }
  }
}
</script>

<style lang="scss" scoped>
.scrollbar-aside {
  border-style: solid;
  border-width: 1px;
  border-color: var(--el-border-color-light);
  border-radius: 4px;
}

.scrollbar-demo-item {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 50px;
  margin: 10px;
  text-align: center;
  border-radius: 4px;
  background: var(--el-color-primary-light-9);
  color: var(--el-color-primary);
}
</style>