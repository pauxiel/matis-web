<template>
  <div v-if="releases.length > 0">
    <div class="row">
      <p class="small font-weight-lighter my-2">{{ countReleases }} items affichés<span class="small"> (en {{processingTime}} s.)</span></p>
    </div>
    <div class="row" v-if="genres && genres.length > 0">
      <span v-for="item in genres" v-bind:key="item.key" class="small">
        <button :id="'btn-genre-'+item.key" type="button" class="btn btn-sm btn-link" :class='item.isClicked == true ? "text-success" : "text-primary"' v-on:click="onSelectGenre(item.key)">{{item.value}}</button>&nbsp;|&nbsp;
      </span>
    </div>
    <div class="row d-flex flex-column">
      <div class="card mb-2"
        v-for="(item, index) in releases"
        v-bind:key="'tracklist-'+item._uid"
        v-show="item.display == true">
        <div class="card-header d-flex justify-content-between" style="background-color: #86a8e2">
          <img v-if="item.author.picture" :src="item.author.picture" class="card-img-top img-fluid rounded" alt="picture artist" style="max-height: 50px; max-width: 50px;">
          <p class="text-capitalize">{{ item.content.type }} by <span class="font-weight-bold">{{ item.author.name }}</span></p>
        </div>
        <div class="card-body text-left">
          <h5 class="card-title text-center">
            <b>#{{index+1}}</b>
            <span class="small align-middle">
              | {{ item.content.title }}
              <i v-if="releaseDays(item.content.updated_at) <= 7" class=" mx-2 d-inline-flex align-middle text-danger medium material-icons">fiber_new</i>
            </span>
          </h5>
          <p class="small text-center">
            <span v-if="releaseDays(item.content.updated_at) > 0">Il y a {{ releaseDays(item.content.updated_at) }} jours ({{ item.content.updated_at }})</span>
            <span v-else-if="releaseDays(item.content.updated_at) < 0">Sortie prévue dans {{ Math.abs(releaseDays(item.content.updated_at)) }} jours ({{ item.content.updated_at }})</span>
            <span v-else-if="releaseDays(item.content.updated_at) === 0">Sortie aujourd'hui</span>
          </p>
          <p class="small font-weight-lighter" v-html="item.content.description"></p>
          <p class="text-center">
            <img style="max-height: 200px; max-width: 100%;" :src="item.content.picture" class="img-fluid rounded" alt="picture artist" v-if="item.content.picture">
          </p>
          <div class="d-flex justify-content-end">
            <button type="button" class="btn btn-link" v-on:click="$emit('showRelease',item)">Voir plus</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ReleasesList',
  props: ['genres', 'releases', 'processingTime'],
  computed: {
    countReleases: function() {
      return this.releases.filter(i => i.display).length;
    }
  },
  methods: {
    releaseDays: function (day) {
      var dateofvisit = this.$moment(day, 'YYYY-MM-DD-MM');
      var today = this.$moment();
      return today.diff(dateofvisit, 'days');
    },
    onSelectGenre(key) {
      if(key) {
        this.$emit('selectGenre', key)
      }
    }
  }
}
</script>
