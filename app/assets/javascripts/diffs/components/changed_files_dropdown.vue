<script>
import Icon from '~/vue_shared/components/icon.vue';
import changedFilesMixin from '../mixins/changed_files';

export default {
  components: {
    Icon,
  },
  mixins: [changedFilesMixin],
  data() {
    return {
      searchText: '',
    };
  },
  computed: {
    filteredDiffFiles() {
      return this.diffFiles.filter(file =>
        file.filePath.toLowerCase().includes(this.searchText.toLowerCase()),
      );
    },
  },
  methods: {
    clearSearch() {
      this.searchText = '';
    },
  },
};
</script>

<template>
  <span>
    Showing
    <button
      class="diff-stats-summary-toggler"
      data-toggle="dropdown"
      type="button"
      aria-expanded="false"
    >
      <span>
        {{ n__('%d changed file', '%d changed files', diffFiles.length) }}
      </span>
      <icon
        :size="8"
        name="chevron-down"
      />
    </button>
    <div class="dropdown-menu diff-file-changes">
      <div class="dropdown-input">
        <input
          v-model="searchText"
          type="search"
          class="dropdown-input-field"
          placeholder="Search files"
          autocomplete="off"
        />
        <i
          v-if="searchText.length === 0"
          aria-hidden="true"
          data-hidden="true"
          class="fa fa-search dropdown-input-search">
        </i>
        <i
          v-else
          role="button"
          class="fa fa-times dropdown-input-search"
          @click="clearSearch"
        ></i>
      </div>
      <div class="dropdown-content">
        <ul>
          <li
            v-for="diffFile in filteredDiffFiles"
            :key="diffFile.name"
          >
            <a
              :href="`#${diffFile.fileHash}`"
              :title="diffFile.newPath"
              class="diff-changed-file"
            >
              <icon
                :name="fileChangedIcon(diffFile)"
                :size="16"
                :class="fileChangedClass(diffFile)"
                class="diff-file-changed-icon append-right-8"
              />
              <span class="diff-changed-file-content append-right-8">
                <strong
                  v-if="diffFile.blob && diffFile.blob.name"
                  class="diff-changed-file-name"
                >
                  {{ diffFile.blob.name }}
                </strong>
                <strong
                  v-else
                  class="diff-changed-blank-file-name"
                >
                  {{ s__('Diffs|No file name available') }}
                </strong>
                <span class="diff-changed-file-path prepend-top-5">
                  {{ truncatedDiffPath(diffFile.blob.path) }}
                </span>
              </span>
              <span class="diff-changed-stats">
                <span class="cgreen">
                  +{{ diffFile.addedLines }}
                </span>
                <span class="cred">
                  -{{ diffFile.removedLines }}
                </span>
              </span>
            </a>
          </li>

          <li
            v-show="filteredDiffFiles.length === 0"
            class="dropdown-menu-empty-item"
          >
            <a>
              {{ __('No files found') }}
            </a>
          </li>
        </ul>
      </div>
    </div>
  </span>
</template>
