<template>
	<div class='styleEditor' ref='container'>
		<div class="code" v-html="codeInStyleTag"></div>
		<div class="" v-html="highlightedCode"></div>
	</div>
</template>

<script type="text/ecmascript-6">
  import Prism from 'prismjs'
  export default {
    name: 'editor',
    props: ['code'],
    computed: {
      highlightedCode: function () {
        return Prism.highlight(this.code, Prism.languages.css)
      },
      codeInStyleTag: function () {
        return `<style>${this.code}</style>`
      }
    },
    methods: {
      goBottom() {
        this.refs.container.scrollTop = 10000
      }
    }
  }
</script>

<style scoped>
  pre{
  }
  @media (max-width:500px){
    pre{
    }
  }
  .code {
    display: none;
  }
</style>