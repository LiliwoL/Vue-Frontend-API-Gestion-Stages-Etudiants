<template>
  <div class="student">
    <h1>Etudiants</h1>

    <div v-if="loading" class="loading">Loading...</div>

    <div v-if="error" class="error">{{ error }}</div>

    <div v-if="students" class="content">
      <h2>Liste des étudiants</h2>

      <table>
        <tr>
          <th>Id</th>
          <th>Photo</th>
          <th>Nom</th>
          <th>Prénom</th>
          <th>Date de naissance</th>
          <th>Classe</th>
        </tr>

        <tbody>
          <tr v-for="(student, index) in students" :key="index">
            <td>{{ student.id }}</td>
            <td>
              <img :src="student.picture" :alt="student.name" />
            </td>
            <td>{{ student.name }}</td>
            <td>{{ student.firstname }}</td>
            <td>{{ formatDate( student.dateOfBirth ) }}</td>
            <td>{{ student.grade }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
// https://router.vuejs.org/guide/advanced/data-fetching.html
export default {
  data() {
    return {
      loading: false,
      error: null,
      students: null,
      apiKey: process.env.VUE_APP_API_KEY
    }
  },
  created() {
    // watch the params of the route to fetch the data again
    /*this.$watch(
        () => this.$route.params,
        () => {
          this.fetchData()
        },
        // fetch the data when the view is created and the data is
        // already being observed
        { immediate: true }
    )*/

    this.fetchData()
  },
  methods: {
    /**
     * Méthode de récupération des données depuis l'API
     * Il faut avant avoir lancé le backend et spécifié l'API-KEY dans le fichier .env
     *
     */
    fetchData() {
      this.error = this.post = null
      this.loading = true

      fetch(
          // LOCAL
          "http://127.0.0.1:8000/api/student",
          {
            method: "GET",
            headers: {
              'Content-Type': 'application/json',
              'API-KEY': process.env.VUE_APP_API_KEY
            }
          }
      )
          .then(response => {
            //console.log(response);
            // Ne pas oublier le return!
            return response.json();
          })
          .then(data => {
            // console.log(data);

            this.students = data;
            this.loading = false;
          })
          .catch(error => {
            console.log("Erreur de récupération API " + error);
            this.error = error.toString()
          });
    },

    /**
     * Formatage d'une date
     *
     * @param unformattedDate
     * @returns {string}
     */
    formatDate( unformattedDate ) {
      let inputDate = new Date( unformattedDate );

      let day, month, year;

      day = inputDate.getDate();
      month = inputDate.getMonth() + 1;
      year = inputDate.getFullYear();

      day = day
          .toString()
          .padStart(2, '0');

      month = month
          .toString()
          .padStart(2, '0');

      return `${day}/${month}/${year}`;
    }
  },
}
</script>