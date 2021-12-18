<template>
  <q-page class="flex flex-center">
    <div class="row">
      <div class="col-grow">
        <div class="q-pa-md" style="max-width: 100vw;">
          <q-expansion-item
            class="shadow-1 overflow-hidden"
            style="border-radius: 30px;"
            icon="terminal"
            label="Raw JSON Data"
            header-class="bg-primary text-white"
            expand-icon-class="text-white"
          >
            <q-card style="background: #2d2d2d;">
              <q-card-section style="overflow: auto; max-height: 80vh;">
                <a
                  :href="`https://api.globalsecuritydatabase.io/${identifier}`"
                  target="_blank"
                  v-if="jsonBlob"
                  style="color: white;"
                >
                  https://api.globalsecuritydatabase.io/{{ identifier }}
                </a>
                <pre class="line-numbers" v-if="jsonBlob"><code class="language-json">{{ jsonBlob }}</code></pre>
                <p v-else style="color: white;">Invalid identifier.</p>
              </q-card-section>
            </q-card>
          </q-expansion-item>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, watch, nextTick } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { api } from 'boot/axios'

// Syntax highlighting for JSON
import Prism from 'prismjs'

export default defineComponent({
  name: 'PageIdentifier',

  setup() {
    const $route = useRoute()
    const $router = useRouter()

    const identifier = $route.params.id
    const jsonBlob = ref('')

    if (identifier.match(/^GSD-\d{4}-\d{7,}$/) || identifier.match(/^UVI-\d{4}-\d{7,}$/)) {
      api.get(`/${identifier}`).then(
        (response) => {
          jsonBlob.value = response.data
        },
        (error) => {}
      )
    }

    watch(
      () => jsonBlob.value,
      (newValue) => {
        nextTick().then(
          () => { Prism.highlightAll() }
        )
      }
    )

    return { jsonBlob, identifier }
  }
})
</script>
