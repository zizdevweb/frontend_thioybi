<template>
  <div>
    <p><span class="badge bg-secondary p-1">Liste des rapports</span></p>
    <ul>
      <li v-for="rapport in rapports" :key="rapport.id">{{ rapport.corps }}</li>
    </ul>

    <p><span class="badge bg-secondary p-1">Créer un nouveau rapport</span></p>
    <input v-model="nouveauRapport.corps" type="text" placeholder="Corps du rapport" class="form-control"/>
    <button @click="creerRapport" class="btn btn-sm btn-primary mt-3">Créer</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      rapports: [],
      nouveauRapport: {
        corps: '',
      },
    };
  },
  methods: {
    async chargerRapports() {
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/rapports');
        this.rapports = response.data;
      } catch (error) {
        console.error('Erreur lors du chargement des rapports :', error);
      }
    },
    async creerRapport() {
      try {
        const response = await axios.post('http://127.0.0.1:8000/api/rapports', this.nouveauRapport);
        this.nouveauRapport.corps = '';
        this.chargerRapports(); 
      } catch (error) {
        console.error('Erreur lors de la création du rapport :', error);
      }
    },
  },
  mounted() {
    this.chargerRapports(); 
  },
};
</script>

<style scoped>


</style>
