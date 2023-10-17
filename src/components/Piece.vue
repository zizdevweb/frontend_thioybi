<template>
  <div>
    <p><span class="badge bg-secondary p-1">Liste des pièces justificatives</span></p>
    <ul>
      <a>
        <ol v-for="piece in pieces" :key="piece.id" class="cursor-pointer" data-bs-toggle="modal" data-bs-target="#exampleModal">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-filetype-pdf text-danger" viewBox="0 0 16 16">
            <path fill-rule="evenodd" d="M14 4.5V14a2 2 0 0 1-2 2h-1v-1h1a1 1 0 0 0 1-1V4.5h-2A1.5 1.5 0 0 1 9.5 3V1H4a1 1 0 0 0-1 1v9H2V2a2 2 0 0 1 2-2h5.5L14 4.5ZM1.6 11.85H0v3.999h.791v-1.342h.803c.287 0 .531-.057.732-.173.203-.117.358-.275.463-.474a1.42 1.42 0 0 0 .161-.677c0-.25-.053-.476-.158-.677a1.176 1.176 0 0 0-.46-.477c-.2-.12-.443-.179-.732-.179Zm.545 1.333a.795.795 0 0 1-.085.38.574.574 0 0 1-.238.241.794.794 0 0 1-.375.082H.788V12.48h.66c.218 0 .389.06.512.181.123.122.185.296.185.522Zm1.217-1.333v3.999h1.46c.401 0 .734-.08.998-.237a1.45 1.45 0 0 0 .595-.689c.13-.3.196-.662.196-1.084 0-.42-.065-.778-.196-1.075a1.426 1.426 0 0 0-.589-.68c-.264-.156-.599-.234-1.005-.234H3.362Zm.791.645h.563c.248 0 .45.05.609.152a.89.89 0 0 1 .354.454c.079.201.118.452.118.753a2.3 2.3 0 0 1-.068.592 1.14 1.14 0 0 1-.196.422.8.8 0 0 1-.334.252 1.298 1.298 0 0 1-.483.082h-.563v-2.707Zm3.743 1.763v1.591h-.79V11.85h2.548v.653H7.896v1.117h1.606v.638H7.896Z"/>
          </svg> {{ piece.name }}

            <!-- Modal -->
            <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
              <div class="modal-dialog">
                <div class="modal-content">
                  <div class="modal-header">
                    <h1 class="modal-title fs-5" id="exampleModalLabel">{{ piece.name }}</h1>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                  </div>
                  <div class="modal-body">
                   <embed src="C:\Users\INROS\Desktop\M1DSGL\micoservices\Projets\thioy-bi-fullstack\public\rapports\lettre de motivation papis 2023.pdf" type="application/pdf" width="100%" height="500px">
                  </div>
                  <div class="modal-footer">
                  </div>
                </div>
              </div>
            </div>
        </ol>
    </a>
    </ul>
  </div>
  
  
  <div>
    <p><span class="badge bg-secondary p-1">Ajouter une nouvelle pièce</span></p>
    <form @submit.prevent="uploadPiece">
      <label>Sélectionner un rapport :</label>
      <select v-model="selectedRapport" class="form-control">
        <option v-for="rapport in rapports" :key="rapport.id" :value="rapport.id">{{ rapport.corps }}</option>
      </select>
      <br>
      <label>Sélectionner un fichier :</label>
      <input type="file" @change="onFileChange" class="form-control" accept=".pdf">
      <br>
      <button type="submit" class="btn btn-sm btn-success">Ajouter la pièce</button>
    </form>
  </div>
</template>

<script>
import axios from 'axios';
import pdfjsLib from 'pdfjs-dist/build/pdf';
export default {
  data() {
    return {
      rapports: [],
      selectedRapport: null,
      pieceFile: null,
      pieces: [],
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
    onFileChange(event) {
      console.log('Fichier sélectionné :', event.target.files[0]);
      this.pieceFile = event.target.files[0];
    },
    ajouterPiece() {
      const formData = new FormData();
      formData.append('rapport_id', this.selectedRapport);
      formData.append('piece', this.pieceFile);
      console.log('piece à ajouter',this.pieceFile)
      console.log('donnees formulaire',formData)
      
      let config = {
        headers : {
        'Content-Type' : 'multipart/form-data'
       }
      }
      axios.post('http://127.0.0.1:8000/api/pieces', formData, config)
        .then(response => {
          if (response.status === 200) {
            
            console.log('Pièce ajoutée avec succès !');
          } else {
            
            console.error('Erreur lors de l\'ajout de la pièce.');
          }
        })
        .catch(error => {
          console.error('Erreur lors de la requête POST :', error);
        });
    },
    async uploadPiece() {
      const formData = new FormData();
      formData.append("rapport_id", this.selectedRapport);
      formData.append("piece", this.pieceFile);
      this.fetchPieces();
      try {
        await axios.post("http://127.0.0.1:8000/api/pieces", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        });

        
      } catch (error) {
        console.error("Erreur lors de l'envoi de la pièce justificative :", error);
      }
    },
    async fetchPieces() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/pieces");
        this.pieces = response.data;
      } catch (error) {
        console.error("Erreur lors de la récupération des pièces justificatives :", error);
      }
    },
  },

  mounted() {
    this.chargerRapports();
    this.fetchPieces();
  },
};
</script>

