<template>
  <main>
    <Header/>
    <Content/>
    <Footer/>
  </main>
</template>

<script>
import Header from './components/Header'
import Footer from './components/Footer'
import Content from './components/Content'

export default {
  name: 'App',
  components: {
    Header,
    Footer,
    Content
  }
}
</script>