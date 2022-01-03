<template>
  <q-page>
    <div class="row items-start justify-evenly q-col-gutter-md q-ma-md">
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

    <div class="row items-start justify-evenly q-col-gutter-md q-ma-md" v-for="namespace in namespaces" :key="namespace">
      <div class="col-grow">
        <q-card bordered>
          <q-card-section class="bg-primary text-white">
            <div class="text-h5">Namespace: "{{ namespace }}"</div>
          </q-card-section>

          <q-separator />

          <q-card-section horizontal>
            <q-markup-table
              class="bg-primary full-width text-center"
              dark
              bordered
              separator="cell"
            >
              <thead class="bg-dark">
                <tr>
                  <th>Key</th>
                  <th>Value</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="key in Object.keys(jsonBlob[namespace])" :key="key">
                  <td class="text-right">{{ key }}</td>
                  <td class="text-left">
                    <div style="max-height: 50vh; max-width: 80vw; overflow: auto;">
                      <template v-if="typeof jsonBlob[namespace][key] === 'string'">
                        <div style="white-space: pre-line;">
                          {{ jsonBlob[namespace][key] }}
                        </div>
                      </template>

                      <template v-else-if="Array.isArray(jsonBlob[namespace][key]) && jsonBlob[namespace][key].every((el) => typeof el === 'string')">
                        <ul>
                          <li v-for="element in jsonBlob[namespace][key]" :key="element">
                            {{ element }}
                          </li>
                        </ul>
                      </template>

                      <template v-else-if="typeof jsonBlob[namespace][key] === 'number'">
                        {{ jsonBlob[namespace][key] }}
                      </template>

                      <template v-else>
                        <pre class="line-numbers" v-if="jsonBlob"><code class="language-json">{{ jsonBlob[namespace][key] }}</code></pre>
                      </template>
                    </div>
                  </td>
                </tr>
              </tbody>
            </q-markup-table>
          </q-card-section>
        </q-card>
      </div>
    </div>

    <div class="row items-start justify-evenly q-col-gutter-md q-ma-md" v-if="cveId && false">
      <div class="col-grow">
        <q-card bordered class="q-pa-md">
          <q-card-section>
            <div class="text-h5">{{ cveId }} - GSD Overlay Data</div>
          </q-card-section>

          <q-separator />

          <q-card-section horizontal>
            <table class="fancy-table">
              <tr>
                <td>Description</td>
                <td><div style="white-space: pre-line;">{{ overlayDescription }}</div></td>
              </tr>
              <tr>
                <td>State</td>
                <td>{{ cveState }}</td>
              </tr>
              <tr>
                <td>Problem Types</td>
                <td>
                  <ul>
                    <li v-for="(problem, index) in cveProblemTypes" :key="index">{{ problem }}</li>
                  </ul>
                </td>
              </tr>
              <tr>
                <td>Vendors, Projects & Versions</td>
                <td>
                  <ul>
                    <li><b>Vendor:</b>&nbsp;{{ cveVendor }}</li>
                    <li><b>Product:</b>&nbsp;{{ cveProduct }}</li>
                    <li><b>Versions Affected:</b>
                      <ul>
                        <li v-for="(version, index) in cveVersionsAffected" :key="index">
                          <b>{{ version.version_name }}:</b> {{ version.version_affected }} {{ version.version_value }}
                        </li>
                      </ul>
                    </li>
                  </ul>
                </td>
              </tr>
              <tr>
                <td>References</td>
                <td>
                  <ul>
                    <li v-for="(reference, index) in overlayReferences" :key="index">
                      <b>{{ reference.refsource }}</b> - <a :href="reference.url" target="_blank">{{ reference.name }}</a>
                    </li>
                  </ul>
                </td>
              </tr>
            </table>
          </q-card-section>
        </q-card>
      </div>
    </div>

    <div class="row items-start justify-evenly q-col-gutter-md q-ma-md" v-if="cveId && false">
      <div class="col-grow">
        <q-card bordered class="q-pa-md">
          <q-card-section>
            <div class="text-h5">{{ cveId }} - Original Data</div>
          </q-card-section>

          <q-separator />

          <q-card-section horizontal>
            <table class="fancy-table">
              <tr>
                <td>Description</td>
                <td>{{ cveDescription }}</td>
              </tr>
              <tr>
                <td>State</td>
                <td>{{ cveState }}</td>
              </tr>
              <tr>
                <td>Problem Types</td>
                <td>
                  <ul>
                    <li v-for="(problem, index) in cveProblemTypes" :key="index">{{ problem }}</li>
                  </ul>
                </td>
              </tr>
              <tr>
                <td>Vendors, Projects & Versions</td>
                <td>
                  <ul>
                    <li><b>Vendor:</b>&nbsp;{{ cveVendor }}</li>
                    <li><b>Product:</b>&nbsp;{{ cveProduct }}</li>
                    <li><b>Versions Affected:</b>
                      <ul>
                        <li v-for="(version, index) in cveVersionsAffected" :key="index">
                          <b>{{ version.version_name }}:</b> {{ version.version_affected }} {{ version.version_value }}
                        </li>
                      </ul>
                    </li>
                  </ul>
                </td>
              </tr>
              <tr>
                <td>References</td>
                <td>
                  <ul>
                    <li v-for="(reference, index) in cveReferences" :key="index">
                      <b>{{ reference.refsource }}</b> - <a :href="reference.url" target="_blank">{{ reference.name }}</a>
                    </li>
                  </ul>
                </td>
              </tr>
            </table>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, watch, nextTick, computed } from 'vue'
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

    const namespaces = computed(
      () => {
        return Object.keys(jsonBlob.value)
      }
    )

    // const gsdOverlayData
    // const cveOriginalData

    const overlayDescription = computed({
      get: () => {
        if(jsonBlob.value["gsd"]["overlay_data"]["cve.org"]) {
          const desc = jsonBlob.value["gsd"]["overlay_data"]["cve.org"]["description"]["description_data"][0]["value"]
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    // TODO: Does not include original references. Should we interpolate? Or update gsd references to include originals? See GSD-2021-1002353 for example of issue.
    const overlayReferences = computed({
      get: () => {
        if(jsonBlob.value["gsd"]["references"]) {
          const desc = jsonBlob.value["gsd"]["references"]
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveDescription = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]?.description?.description_data[0]?.value
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveId = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["CVE_data_meta"]["ID"];
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveState = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["CVE_data_meta"]["STATE"];
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveProblemTypes = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["problemtype"]["problemtype_data"].map(
            (val) => {
              return val.description[0].value
            }
          );
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveVendor = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["affects"]["vendor"]["vendor_data"][0]["vendor_name"]
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveProduct = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["affects"]["vendor"]["vendor_data"][0]["product"]["product_data"][0]["product_name"]
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveVersionsAffected = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["affects"]["vendor"]["vendor_data"][0]["product"]["product_data"][0]["version"]["version_data"]
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    const cveReferences = computed({
      get: () => {
        if(jsonBlob.value["cve.org"]) {
          const desc = jsonBlob.value["cve.org"]["references"]["reference_data"]
          return desc ? desc : 'Error - Description unavailable.'
        }

        return ''
      }
    })

    watch(
      () => jsonBlob.value,
      (newValue) => {
        nextTick().then(
          () => { Prism.highlightAll() }
        )
      }
    )

    return {
      jsonBlob,
      identifier,
      namespaces,
      cveId,
      cveDescription,
      cveState,
      cveProblemTypes,
      cveVendor,
      cveProduct,
      cveVersionsAffected,
      cveReferences,
      overlayDescription,
      overlayReferences,
    }
  }
})
</script>

<style lang="sass">
// FIXME: I hate css, and it hates me. Please make this look prettier.
.fancy-table
  width: 100%
  border: 1px solid
  border-collapse: collapse
  td
    border: 1px solid #ddd
    padding: 8px
  tr:nth-child(even)
    background-color: #f2f2f2
  td:hover // I'm very tempted to remove this hover effect, tbh.
    background-color: #ddd
  td:nth-child(odd)
    background-color: #0b2f5e
    color: white
    font-weight: bold
</style>
