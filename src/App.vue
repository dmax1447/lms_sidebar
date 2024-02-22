<template>
  <div id="app">
<!--    <nav>-->
<!--      <ul>-->
<!--        <li>-->
<!--          <router-link to="/courses">Курсы</router-link>-->
<!--          <ul>-->
<!--            <li><router-link to="/courses/stat">Статистика</router-link></li>-->
<!--            <ul>-->
<!--              <li>-->
<!--                <router-link-->
<!--                    to="/courses/stat/assignment"-->
<!--                    class="menu-link"-->
<!--                    :class="{-->
<!--                      disabled: menu.disabled.assignment,-->
<!--                      hidden: menu.hidden.assignment-->
<!--                    }"-->
<!--                >Задания</router-link></li>-->
<!--              <li><router-link to="/courses/stat/quiz">Тесты</router-link></li>-->
<!--            </ul>-->

<!--          </ul>-->
<!--        </li>-->
<!--        <li>-->
<!--          <router-link to="/tests">Тесты</router-link>-->
<!--        </li>-->
<!--        <li>-->
<!--          <router-link to="/kit">Kit</router-link>-->
<!--        </li>-->
<!--      </ul>-->
<!--    </nav>-->
<!--    <hr>-->
    <nav>
      <ul>
        <li v-for="rootLevel in nav" :key="rootLevel.id">
          <router-link :to="rootLevel.path">{{rootLevel.title}}</router-link>
          <ul v-if="rootLevel.children">
            <li v-for="rootLevelChild in rootLevel.children" :key="rootLevelChild.id"><router-link to="/courses/stat">Статистика</router-link>
              <ul v-if="rootLevelChild.children">
                <li v-for="nestedLevelChild in rootLevelChild.children" :key="nestedLevelChild.id">
                  <router-link
                      :to="nestedLevelChild.path"
                      class="menu-link"
                      :class="{
                      disabled: menu.disabled[nestedLevelChild.id],
                      hidden: menu.hidden[nestedLevelChild.id]
                    }"
                  >{{ nestedLevelChild.title }}</router-link></li>
              </ul>
            </li>
          </ul>
        </li>
      </ul>
    </nav>
  </div>
</template>
<script>

export default {
  name: 'App',
  data() {
    return {
      menu: {
        disabled: {},
        hidden: {},
      },
      nav: [
        {
          path: '/courses', id: 'courses', title: 'Курсы',
          children: [
            {
              path: '/courses/stat', id: 'courses|stat', title: 'Статистика',
              children: [
                {path: '/courses/stat/assignment', id: 'assignment', title: 'Задания',},
                {path: '/courses/stat/quiz', id: 'courses|stat|tests', title: 'Тесты',}
              ]
            }
          ]
        },
        {
          path: '/tests', id: 'tests', title: 'Тесты'
        },
        {
          path: '/kit', id: 'kit', title: 'Ui-kit'
        }
      ]



    }
  },

  created() {
    this.$root.eventBus.on('sidebar:hide_item', (e) => {
      console.log('sidebar:hide_item', e)
      if (!Object.hasOwn(this.menu.hidden, e.id)) {
        this.$set(this.menu.hidden, e.id, true)
        return
      }
      this.menu.hidden[e.id] = !this.menu.hidden[e.id]
    })
    this.$root.eventBus.on('sidebar:disable_item', (e) => {
      if (!Object.hasOwn(this.menu.disabled, e.id)) {
        this.$set(this.menu.disabled, e.id, true)
        return
      }
      this.menu.disabled[e.id] = !this.menu.disabled[e.id]
    })
  }
}
</script>

<style lang="scss" scoped>


nav {
  display: flex;
  flex-direction: column;
  gap: 20px;
  ul {
    list-style: none;
    padding-left: 16px;
    margin-bottom: 20px;
  }
  li > ul {
    margin-top: 10px;
  }

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
    &.hidden {
      color: red !important;
      text-decoration: none;
      display: none;
    }
    &.disabled {
      color: lightgrey !important;
      text-decoration: none;
    }

  }
}
</style>
