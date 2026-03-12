<template>
  <q-item
    clickable
    tag="a"
    :href="link"
    class="rounded-borders q-mx-sm q-my-xs"
  >
    <q-item-section avatar>
      <q-icon :name="icon" color="primary" />
    </q-item-section>

    <q-item-section>
      <q-item-label>{{ title }}</q-item-label>
      <q-item-label caption v-if="caption">
        {{ caption }}
      </q-item-label>
    </q-item-section>

  </q-item>
</template>

<script>
import { defineComponent } from "vue"

export default defineComponent({
  name: "EssentialLink",

  props: {
    title: String,
    caption: String,
    link: String,
    icon: String
  }
})
</script>