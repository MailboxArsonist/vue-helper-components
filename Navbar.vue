<template>
  <nav
    class="nav"
    :class="{
      'nav-minimized': minimizeNav
    }"
    @scroll="handleScroll"
  >
    <!-- Logo-->
    <router-link to="/">
      <img :src="logo.path" alt="logo" class="logo" />
    </router-link>

    <!-- Links -->
    <ul class="nav-links">
      <li class="nav-link" v-for="link in links" :key="link.text">
        <router-link :to="link.href"> {{ link.text }} </router-link>
      </li>
    </ul>

    <!-- Mobile side menu -->
    <div class="mobile-links">
      <!-- Put burger here -->
      <button class="nav-button" @click="handleMenuClick">
        <img :src="burger.path" alt="menu" />
      </button>
      <div
        class="panel"
        :class="{
          active: sideMenuOpen
        }"
      >
        <ul class="nav-mobile-links">
          <li class="nav-link">
            <button class="nav-button">
              <img
                src="~/assets/img/svg/cancel.svg"
                alt="close"
                @click="closeMenu"
              />
            </button>
          </li>
          <li class="nav-link" v-for="link in links" :key="link.text">
            <router-link :to="link.href"> {{ link.text }} </router-link>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  name: "NavBar",
  props: {
    links: {
      type: Array,
      default: null
    },
    logo: {
      type: Object,
      default: null
    },
    burger: {
      type: Object,
      default: null
    },
    burger: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      minimizeNav: false,
      sideMenuOpen: false
    };
  },
  mounted() {
    window.addEventListener("scroll", this.handleScroll);
    if (window.scrollY > 50) {
      this.minimizeNav = true;
    } else if (document.body.scrollTop < 50) {
      this.minimizeNav = false;
    }
  },
  destroyed() {
    window.removeEventListener("scroll", this.handleScroll);
  },
  methods: {
    handleScroll() {
      if (window.scrollY > 50) {
        this.minimizeNav = true;
      } else if (document.body.scrollTop < 50) {
        this.minimizeNav = false;
      }
    },
    handleMenuClick() {
      this.sideMenuOpen = true;
    },
    closeMenu() {
      this.sideMenuOpen = false;
    }
  }
};
</script>

<style lang="scss" scoped>
.nav {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;

  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 10;
  height: 9em;
  color: black;
  border-bottom: 1px solid transparent;
  background: transparent;
  transition: height 700ms;

  .logo {
    height: auto;
    width: 240px;
    margin-left: 2em;
    min-width: 200px;
  }
}

.nav::after {
  clear: both;
  content: " ";
}

.nav-minimized {
  height: 75px;
  box-shadow: 0 10px 20px -4px rgba(0, 0, 0, 0.03);
  border-color: #fff;
  background-color: #fff;
  transition: height 700ms;
}

.nav-links {
  width: 100%;
  margin: 0;
  padding: 1em;
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
  color: inherit;
}

.nav-link {
  list-style: none;
  display: inline;
  margin-right: 5em;
  a {
    font-size: 1.2em;
    font-weight: 600;
    border-bottom: 1px solid transparent;
    position: relative;
    padding-left: 2em;
  }
}

.mobile-links {
  display: none;
  margin: 0 2em 0 auto;
  .nav-button {
    width: 20px;
    height: 20px;
    border: 0;
    cursor: pointer;
    background: none;
    img {
      width: 100%;
      height: auto;
    }
  }
  .panel {
    position: fixed;
    left: 100%;
    height: 100%;
    top: 0;
    background-color: rgba(102, 100, 100, 0.8);

    &.active {
      left: 0;
      width: 100%;
    }
  }
  .nav-mobile-links {
    background: #fff;
    height: 100%;
    margin: 0;
    margin-left: 20%;
    padding: 2em;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    color: inherit;
    .nav-link {
      margin-bottom: 1em;
      margin-right: 0;
      &:first-child {
        text-align: right;
      }
    }
  }
}

@media (max-width: 900px) {
  .mobile-links {
    display: block;
  }
  .nav-links {
    display: none;
  }
}
@media (max-width: 500px) {
  .nav {
    height: 7em;
    .logo {
      width: 185px;
      min-width: 110px;
      margin-left: 1em;
    }
  }
  .mobile-links {
    margin-right: 1em;
  }
}
</style>
