<template>
  <div class="author">
 
    <table class="table">
      <caption></caption>
      <tbody>
      <tr>
        <th>User login</th>
         <th>Email</th>
      </tr>

      <tr>
      <td>{{userItem.login}}</td>
      <td>{{userItem.email}}</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  props: ['userItem']
}
</script>
<style scoped>
.author{
  text-align: center;
}
.table{
  width: 700px;
  margin: auto;
  border: 2px dashed red;
}
.table tr th{
  text-align: center;
   border: 2px solod pink;
}
.title{
  color: blue;
  font-size: 18px;
}
</style>