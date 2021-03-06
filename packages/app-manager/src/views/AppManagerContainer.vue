<template>
  <div class="app-manager-container mt-2">
    <!-- Hidden file upload -->
    <input
      id="app-upload-input-field"
      @change="handleFileUpload"
      ref="app-upload-field"
      type="file"
      v-show="false"
      accept=".gz, .zip"
    />

    <div class="container">
      <div class="row text-center">
        <div class="col-12">
          <template v-if="error">
            <div class="alert alert-warning" role="alert">{{ error }}</div>
          </template>
        </div>
      </div>

      <template v-if="loading">
        <div class="row">
          <div class="col-12">
            <div
              class="loading-spinner-container d-flex flex-column justify-content-center align-items-center"
            >
              <i class="fa fa-spinner fa-spin fa-5x my-3"></i>
              <p class="text-muted">Fetching apps...</p>
            </div>
          </div>
        </div>
      </template>

      <template v-else>
        <div class="row">
          <div class="col-12">
            <div class="d-flex justify-content-center pt-4">
              <button class="app-upload-btn btn btn-lg btn-success" @click="triggerFileBrowser">
                <i class="fa fa-cloud-upload"></i> Upload new app
              </button>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-12">
            <div class="d-flex justify-content-center p-4">
              <p class="text-muted">
                <small>
                  Zip should contain
                  <em>index.html</em> and
                  <em>config.json</em>, consult
                  <a
                    target="_blank"
                    rel="noopener noreferrer"
                    href="https://molgenis.gitbook.io/molgenis/for-developers/guide-development-apps"
                  >the manual</a>
                  for details.
                </small>
              </p>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-12">
            <div class="mb-4 d-flex justify-content-center">
              <div class="input-group">
                <div class="input-group-prepend">
                  <span class="input-group-text" id="search-icon">
                    <i class="fa fa-search"></i>
                  </span>
                </div>
                <input
                  v-model="searchQuery"
                  class="form-control search-box"
                  type="search"
                  placeholder="Search apps"
                  aria-label="Search apps"
                  aria-describedby="search-icon"
                />
              </div>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-12">
            <div class="app-card-container">
              <template v-if="apps.length === 0">No apps were found</template>

              <template v-else>
                <app-card-gallery :apps="apps"></app-card-gallery>
              </template>
            </div>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>

<style>
.search-box {
  border-radius: 10px
}

.loading-spinner-container {
  height: 30vh
}
</style>

<script>
import AppCardGallery from '@/components/AppCardGallery.vue'
import filterArray from '@/utils/filter-array'

export default {
  name: 'AppManagerContainer',
  data () {
    return {
      searchQuery: ''
    }
  },
  computed: {
    apps () {
      return filterArray(this.$store.state.apps, this.searchQuery)
    },

    error () {
      return this.$store.state.error
    },

    loading () {
      return this.$store.state.loading
    }
  },
  methods: {
    triggerFileBrowser () {
      // Trigger file upload by clicking the hidden input
      document.getElementById('app-upload-input-field').click()
    },

    handleFileUpload (event) {
      this.$store.commit('SET_ERROR', '')
      const file = event.target.files[0]
      this.$store.dispatch('UPLOAD_APP', file)

      // Clear the existing value to make it possible to upload the same file again
      this.$refs['app-upload-field'].value = ''
    }
  },
  created () {
    this.$store.dispatch('FETCH_APPS')
  },
  components: {
    AppCardGallery
  }
}
</script>
