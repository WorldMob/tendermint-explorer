<template lang="pug">
tm-page(title='Transaction Search')
  tm-part(title='Search with transaction hash')
    tm-form-struct(:submit="search")
      tm-form-group
        .tm-modal-search
          tm-field#search-input(
            type="text"
            placeholder="Search for tx"
            required
            v-model="query"

            title="1 to 60 characters")
          tm-btn(type="submit" icon="search" value="Go")

  tm-part(title='Sample Queries')
    // tm-list-item(dt='Address' dd='3HNSiAq7wFDaPsYDcUxNSRMD78qVcYKicw' @click.native="fillField('3HNSiAq7wFDaPsYDcUxNSRMD78qVcYKicw')")
    tm-list-item(dt='Transaction' dd='5AB68BCCD4DB70CED0F7E84CBCA089229D968833108E2855F8C5FA84808CFBD3' @click.native="fillField('5AB68BCCD4DB70CED0F7E84CBCA089229D968833108E2855F8C5FA84808CFBD3')")
    //tm-list-item(dt='Block #' dd='1337' @click.native="fillField('1337')")
</template>

<script>
import { mapGetters } from "vuex"
import {
  TmListItem,
  TmFormGroup,
  TmFormStruct,
  TmPage,
  TmPart,
  TmBtn,
  TmField
} from "@tendermint/ui"
export default {
  name: "page-search",
  components: {
    TmBtn,
    TmField,
    TmFormGroup,
    TmFormStruct,
    TmListItem,
    TmPage,
    TmPart
  },
  computed: {
    ...mapGetters(["bc"])
  },
  data: () => ({
    query: ""
  }),
  methods: {
    fillField(value) {
      this.query = value
    },
    search() {
      // if (parseInt(this.query)) {
        this.$router.push({ name: "tx", params: { hash: this.query } })
      // } else {
      //   console.log("Only # queries are supported right now", this.query)
      // }
    }
  }
}
</script>
<style lang="stylus">
.tm-modal-search
  display: flex;
  flex: 1;
  .tm-field
    width: auto;
    flex: 1;
  .tm-btn
    margin-left: 0.5rem;
</style>
