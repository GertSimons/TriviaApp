<template>
  <div>
    <b-nav tabs>
      <b-nav-item disabled><b>Trivia App</b></b-nav-item>
      <b-nav-item disabled>Counter {{numberCorrect}}/{{numberTotal}}</b-nav-item>
    </b-nav>
  </div>
</template>

<script>
export default {
  props: [
    'numberCorrect',
    'numberTotal'
  ]
}
</script>
