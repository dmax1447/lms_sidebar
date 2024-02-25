<template>
  <div id="app">
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
    <div>
      <input class="input" type="text" placeholder="message" v-model="message">
      <SuperButton @click.native="send">send</SuperButton>
    </div>
  </div>
</template>
<script>
import { emitter, SuperButton } from '@lms/styleguide'
import { routes } from '@lms/courses'
console.log('sidebar:course_routes',routes)

export default {
  name: 'App',
  components: {
    SuperButton
  },
  data() {
    return {
      message: '',
      menu: {
        disabled: {},
        hidden: {},
      },
      nav: []
    }
  },
  methods: {
    send() {
      emitter.emit('message', this.message)
      this.message = ''
    }
  },
  created() {
    console.log('sidebar:$root', this.$root)
    this.nav = this.$root.nav
    emitter.on('sidebar:hide_item', (e) => {
      console.log('sidebar:hide_item', e)
      if (!Object.hasOwn(this.menu.hidden, e.id)) {
        this.$set(this.menu.hidden, e.id, true)
        return
      }
      this.menu.hidden[e.id] = !this.menu.hidden[e.id]
    })
    emitter.on('sidebar:disable_item', (e) => {
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

.input {
  width: 100%;
  margin-bottom: 10px;
}

</style>
