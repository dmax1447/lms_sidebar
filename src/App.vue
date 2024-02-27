<template>
  <div id="app">
    <nav>
      <ul>
        <li v-for="rootLevel in nav" :key="rootLevel.id">
          <router-link v-if="rootLevel.type === 'internal'" :to="`/${rootLevel.id}`">{{rootLevel.title}}</router-link>
          <a v-if="rootLevel.type === 'link'" :href="rootLevel.path" target="_blank">{{rootLevel.title}}</a>

          <ul v-if="rootLevel.children">
            <li v-for="rootLevelChild in rootLevel.children" :key="rootLevelChild.path">
              <router-link
                  :to="rootLevelChild.path"
                  :class="{
                      disabled: isNavItemDisabled({rootId: rootLevel.id, name: rootLevelChild.name}),
                      hidden: isNavItemHidden({rootId: rootLevel.id, name: rootLevelChild.name}),
                    }"
              >{{rootLevelChild.meta.title}}</router-link>
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

export default {
  name: 'SidebarApp',
  components: {
    SuperButton
  },
  data() {
    return {
      message: '',
      navState: {
        disabled: [],
        hidden: [],
      },
      nav: [],
    }
  },
  methods: {
    send() {
      emitter.emit('message', this.message)
      this.message = ''
    },
    isNavItemDisabled({rootId, name}) {
      return this.navState.disabled.some(el => el.rootId === rootId && el.name === name)
    },
    isNavItemHidden({rootId, name}) {
      return this.navState.hidden.some(el => el.rootId === rootId && el.name === name)
    }

  },
  created() {
    emitter.on('sidebar:hide_item', ({rootId, name}) => {
      if (this.isNavItemHidden({rootId, name})) {
        this.navState.hidden = this.navState.hidden.filter(el => !(el.rootId === rootId && el.name === name))
      } else {
        this.navState.hidden.push({rootId, name})
      }
    })
    emitter.on('sidebar:disable_item', ({rootId, name}) => {
      if (this.isNavItemDisabled({rootId, name})) {
        this.navState.disabled = this.navState.disabled.filter(el => !(el.rootId === rootId && el.name === name))
      } else {
        this.navState.disabled.push({rootId, name})
      }
    })
    this.$root.sidebarNav.subscribe((val) => {
      this.nav = val
    })
  },
  beforeDestroy() {
    this.$root.sidebarNav.unsubscribe()
    emitter.off('sidebar:hide_item')
    emitter.off('sidebar:disable_item')
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
