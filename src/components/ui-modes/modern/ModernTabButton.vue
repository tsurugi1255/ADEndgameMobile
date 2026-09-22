<script>
export default {
  name: "ModernTabButton",
  props: {
    tab: {
      type: Object,
      required: true
    },
    tabPosition: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      isAvailable: false,
      isHidden: false,
      subtabVisibilities: [],
      showSubtabs: false,
      hasNotification: false,
      tabName: "",
      uiClass: ""
    };
  },
  computed: {
    classObject() {
      return {
        "o-tab-btn": true,
        "o-tab-btn--modern-tabs": true,
        "o-tab-btn--subtabs": this.showSubtabs,
        "o-tab-btn--active": this.isCurrentTab && Theme.currentName() !== "S9"
      };
    },
    isCurrentTab() {
      return this.tab.isOpen;
    }
  },
  methods: {
    update() {
      this.isAvailable = this.tab.isAvailable;
      this.isHidden = this.tab.isHidden;
      this.subtabVisibilities = this.tab.subtabs.map(x => x.isAvailable);
      this.showSubtabs = this.isAvailable && this.subtabVisibilities.length >= 1;
      this.hasNotification = this.tab.hasNotification;
      this.uiClass = (typeof this.tab.config.UIClass === "function") ? this.tab.config.UIClass() : this.tab.config.UIClass;
      if (this.tabPosition < Pelle.endTabNames.length) {
        this.tabName = Pelle.transitionText(
          this.tab.name,
          Pelle.endTabNames[this.tabPosition],
          Math.clamp(GameEnd.endState - (this.tab.id % 4) / 10, 0, 1)
        );
      } else {
        this.tabName = this.tab.name;
      }
    },
    isCurrentSubtab(id) {
      return player.options.lastOpenSubtab[this.tab.id] === id && Theme.currentName() !== "S9";
    }
  },
};
</script>

<template>
  <div
    v-if="!isHidden && isAvailable"
    :class="[classObject, uiClass]"
  >
    <div
      class="l-tab-btn-inner"
      @click="tab.show(true)"
    >
      {{ tabName.substring(0,1) }}
      <div
        v-if="hasNotification"
        class="fas fa-circle-exclamation l-notification-icon"
      />
    </div>
    <div
      v-if="showSubtabs && isCurrentTab"
      class="subtabs"
    >
      <template
        v-for="(subtab, index) in tab.subtabs"
      >
        <div
          v-if="subtabVisibilities[index]"
          :key="index"
          class="o-tab-btn o-tab-btn--subtab"
          :class="
            [uiClass,
             {'o-subtab-btn--active': isCurrentSubtab(subtab.id)}]
          "
          @click="subtab.show(true)"
        >
          {{ subtab.name }}
          <div
            v-if="subtab.hasNotification"
            class="fas fa-circle-exclamation l-notification-icon"
          />
        </div>
      </template>
    </div>
  </div>
</template>

<style scoped>

.o-tab-btn--cd-expansion::before {
  background: linear-gradient(var(--color-infinity), var(--color-eternity), var(--color-reality), var(--color-celestials));
}
  
.o-tab-btn--universes::before {
  animation: a-universes-glow-hover 10s infinite;
}

.o-tab-btn--universes__transient::before {
  animation: a-universes--transient-glow-hover 10s infinite;
}

.o-tab-btn--active {
  border-bottom-width: 0.5rem;
}

.o-subtab-btn--active {
  border-bottom-width: 0.5rem solid var(--color-accent);
}

.o-tab-btn--subtab:first-child {
  border-top-left-radius: var(--var-border-radius, 0.5rem);
  border-bottom-left-radius: var(--var-border-radius, 0.5rem);
  transition: border-radius 0s;
}

.o-tab-btn--subtab:last-child {
  border-top-right-radius: var(--var-border-radius, 0.5rem);
  border-bottom-right-radius: var(--var-border-radius, 0.5rem);
  transition: border-radius 0s;
}
</style>
