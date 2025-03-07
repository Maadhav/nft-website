<template>
  <footer class="page-edit">
    <div
      v-if="editLink"
      class="edit-link"
    >
      <a
        :href="editLink"
        target="_blank"
        rel="noopener noreferrer"
      >{{ editLinkText }}</a>
      <OutboundLink />
    </div>

    <div
      v-if="lastUpdated"
      class="last-updated"
    >
      <span class="prefix">{{ lastUpdatedText }}:</span>
      <span class="time">{{ lastUpdated }}</span>
      <a
        href="https://github.com/protocol/nft-website/blob/main/docs/reference/nft-marketplaces.md"
        target="_blank"
        rel="noopener noreferrer"
      >
        by NFT School contributors like you
      </a>
    </div>
  </footer>
</template>

<script>
import isNil from 'lodash/isNil'
import { endingSlashRE, outboundRE } from '@parent-theme/util'

export default {
  name: 'PageEdit',
  computed: {
    lastUpdated () {
      return this.$page.lastUpdated
    },
    lastUpdatedText () {
      if (typeof this.$themeLocaleConfig.lastUpdated === 'string') {
        return this.$themeLocaleConfig.lastUpdated
      }
      if (typeof this.$site.themeConfig.lastUpdated === 'string') {
        return this.$site.themeConfig.lastUpdated
      }
      return 'Last Updated'
    },
    editLink () {
      const showEditLink = isNil(this.$page.frontmatter.editLink)
        ? this.$site.themeConfig.editLinks
        : this.$page.frontmatter.editLink
      const {
        repo,
        docsDir = '',
        docsBranch = 'master',
        docsRepo = repo
      } = this.$site.themeConfig
      if (showEditLink && docsRepo && this.$page.relativePath) {
        return this.createEditLink(
          repo,
          docsRepo,
          docsDir,
          docsBranch,
          this.$page.relativePath
        )
      }
      return null
    },
    editLinkText () {
      return (
        this.$themeLocaleConfig.editLinkText
        || this.$site.themeConfig.editLinkText
        || `Edit this page`
      )
    }
  },
  methods: {
    createEditLink (repo, docsRepo, docsDir, docsBranch, path) {
      const bitbucket = /bitbucket.org/
      if (bitbucket.test(docsRepo)) {
        const base = docsRepo
        return (
          base.replace(endingSlashRE, '')
          + `/src`
          + `/${docsBranch}/`
          + (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '')
          + path
          + `?mode=edit&spa=0&at=${docsBranch}&fileviewer=file-view-default`
        )
      }
      const gitlab = /gitlab.com/
      if (gitlab.test(docsRepo)) {
        const base = docsRepo
        return (
          base.replace(endingSlashRE, '')
          + `/-/edit`
          + `/${docsBranch}/`
          + (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '')
          + path
        )
      }
      const base = outboundRE.test(docsRepo)
        ? docsRepo
        : `https://github.com/${docsRepo}`
      return (
        base.replace(endingSlashRE, '')
        + '/edit'
        + `/${docsBranch}/`
        + (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '')
        + path
      )
    }
  }
}
</script>

<style lang="stylus">
.page-edit
  max-width $contentWidth
  margin 0 auto
  padding 2rem 2.5rem
  @media (max-width: $MQNarrow)
    padding 2rem
  @media (max-width: $MQMobileNarrow)
    padding 1.5rem

  overflow auto
  .edit-link
    display inline-block
    a
      color lighten($textColor, 25%)
      margin-right 0.25rem
  .last-updated
    float right
    font-size 0.9em
    .prefix
      font-weight 500
      color lighten($textColor, 25%)
    .time
      font-weight 400
      color #767676
@media (max-width: $MQMobile)
  .page-edit
    .edit-link
      margin-bottom 0.5rem
    .last-updated
      font-size 0.8em
      float none
      text-align left
</style>