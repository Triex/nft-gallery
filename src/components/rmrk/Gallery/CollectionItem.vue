<template>
  <div class="pack-item-wrapper container is-fluid">
    <div class="columns is-centered">
      <div class="column is-half has-text-centered">
        <div class="container image is-128x128 mb-2">
          <b-image
            v-if="!isLoading"
            :src="image"
            :alt="name"
            ratio="1by1"
            rounded
          ></b-image>
        </div>
        <h1 class="title is-2">
          {{ name }}
        </h1>
      </div>
    </div>

    <div class="columns">
      <div class="column">
        <div class="label">
          {{ $t('creator') }}
        </div>
        <div class="subtitle is-size-6">
          <ProfileLink :address="issuer" :inline="true" :showTwitter="true"/>
        </div>
      </div>
      <div class="column" v-if="owner">
        <div class="label">
          {{ $t('owner') }}
        </div>
        <div class="subtitle is-size-6">
          <ProfileLink :address="owner" :inline="true" :showTwitter="true" />
        </div>
      </div>
      <div class="column is-2">
        <Sharing v-if="sharingVisible"
          class="mb-2"
          label="Check this awesome Collection on %23KusamaNetwork %23KodaDot"
          :iframe="iframeSettings" />
        <DonationButton :address="issuer" style="width: 100%;" />
      </div>
    </div>

    <CollectionActivity :nfts="collection.nfts" />

    <div class="columns is-centered">
      <div class="column is-8 has-text-centered">
        <VueMarkdown :source="description" />
      </div>
    </div>

    <Search v-bind.sync="searchQuery" />

    <GalleryCardList :items="collection.nfts" />

  </div>
</template>

<script lang="ts" >
import { emptyObject } from '@/utils/empty'
import { notificationTypes, showNotification } from '@/utils/notification'
import { Component, Vue } from 'vue-property-decorator'
import { CollectionWithMeta, Collection } from '../service/scheme'
import { sanitizeIpfsUrl, fetchCollectionMetadata } from '../utils'
import isShareMode from '@/utils/isShareMode'
import collectionById from '@/queries/collectionById.graphql'
import { CollectionMetadata } from '../types'
import { NFT } from '@/components/rmrk/service/scheme'
import { SearchQuery } from './Search/types'


const components = {
  GalleryCardList: () => import('@/components/rmrk/Gallery/GalleryCardList.vue'),
  CollectionActivity: () => import('@/components/rmrk/Gallery/CollectionActivity.vue'),
  Sharing: () => import('@/components/rmrk/Gallery/Item/Sharing.vue'),
  ProfileLink: () => import('@/components/rmrk/Profile/ProfileLink.vue'),
  VueMarkdown: () => import('vue-markdown-render'),
  Search: () => import('./Search/SearchBarCollection.vue'),
  DonationButton: () => import('@/components/transfer/DonationButton.vue'),
}
@Component<CollectionItem>({
  metaInfo() {
    return {
      title: 'KodaDot cares about environmental impact',
      titleTemplate: '%s | Low Carbon NFTs',
      meta: [
        { name: 'description', content: 'Creating Carbonless NFTs on Kusama' },
        { property: 'og:title', content: this.collection.name || 'KodaDot cares about environmental impact'},
        { property: 'og:url', content: 'https://nft.kodadot.xyz/' + this.$route.path },
        { property: 'og:image', content: this.meta.image || 'https://nft.kodadot.xyz/kodadot_carbonless.jpg'},
        { property: 'og:description', content: this.meta.description || 'Creating Carbonless NFTs on Kusama'},
        { property: 'twitter:card', content: 'summary_large_image' },
        { property: 'twitter:title', content: this.collection.name || 'KodaDOT cares about environmental impact'},
        { property: 'twitter:description', content: this.meta.description || 'Creating Carbonless NFTs on Kusama'},
        { property: 'twitter:image', content: this.meta.image || 'https://nft.kodadot.xyz/kodadot_carbonless.jpg'},
      ]
    }
  },
  components })
export default class CollectionItem extends Vue {
  private id = '';
  private collection: CollectionWithMeta = emptyObject<CollectionWithMeta>();
  private isLoading = false;
  public meta: CollectionMetadata = emptyObject<CollectionMetadata>();
  private searchQuery: SearchQuery = {
    search: '',
    type: '',
    sortBy: 'BLOCK_NUMBER_DESC',
    listed: false,
  };

  get image(): string {
    return this.meta.image || ''
  }

  get description(): string {
    return this.meta.description || ''
  }

  get name(): string {
    return this.collection.name || this.id
  }

  get nfts(): NFT[] {
    return this.collection.nfts || []
  }

  get issuer(): string {
    return this.collection.issuer || ''
  }

  get owner(): string {
    return this.collection.issuer === (this.collection as any).currentOwner ? '' : (this.collection as any).currentOwner
  }

  get sharingVisible(): boolean {
    return !isShareMode
  }

  private buildSearchParam(): Record<string, unknown>[] {
    const params = []

    if (this.searchQuery.search) {
      params.push({
        name: { likeInsensitive: `%${this.searchQuery.search}%` }
      })
    }

    if (this.searchQuery.listed) {
      params.push({
        price: { greaterThan: '0' }
      })
    }

    return params
  }

  public created() {
    this.isLoading = true
    this.checkId()
    this.$apollo.addSmartQuery('collection', {
      query: collectionById,
      variables: () => {
        return {
          id: this.id,
          orderBy: this.searchQuery.sortBy,
          search: this.buildSearchParam()
        }
      },
      update: ({ collectionEntity }) => { return { ...collectionEntity, nfts: collectionEntity.nfts.nodes } },
      result: () => this.fetchMetadata(),
    })
    this.isLoading = false
  }

  public async fetchMetadata() {
    console.log(this.collection['metadata'], !this.meta['image'])
    if (this.collection['metadata'] && !this.meta['image']) {
      const meta = await fetchCollectionMetadata(this.collection)
      this.meta = {
        ...meta,
        image: sanitizeIpfsUrl(meta.image || ''),
      }
    }
  }

  public checkId() {
    if (this.$route.params.id) {
      this.id = this.$route.params.id
    }
  }

  get iframeSettings() {
    return { width: '100%', height: '100vh' }
  }

  collectionMeta(collection: Collection) {
    fetchCollectionMetadata(collection)
      .then(
        meta => this.collection = {
          ...collection,
          ...meta,
          image: sanitizeIpfsUrl(meta.image || ''),
        },
        e => {
          showNotification(`${e}`, notificationTypes.danger)
          console.warn(e)
        }
      )
  }
}
</script>
