<template>
  <div class="company">
    <h1>Entreprises</h1>

    <div v-if="loading" class="loading">Loading...</div>

    <div v-if="error" class="error">{{ error }}</div>

    <div v-if="companies" class="content">
      <h2>Liste des entreprises</h2>

      <table>
        <tr>
          <th>Id</th>
          <th>Nom</th>
          <th>Rue</th>
          <th>Code postal</th>
          <th>Ville</th>
          <th>Site web</th>
        </tr>

        <tbody>
          <tr v-for="(company, index) in companies" :key="index">
            <td>{{ company.id }}</td>
            <td>{{ company.name }}</td>
            <td>{{ company.street }}</td>
            <td>{{ company.zipcode }}</td>
            <td>{{ company.city }}</td>
            <td>
              <a :href="company.website" />
            </td>
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
      companies: null,
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
          "http://127.0.0.1:8000/api/company",
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

            this.companies = data;
            this.loading = false;
          })
          .catch(error => {
            console.log("Erreur de récupération API " + error);
            this.error = error.toString()
          });
    }
  },
}
</script>