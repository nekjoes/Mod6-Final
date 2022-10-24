<template>
   <div >
    <h4>Empresa FyF</h4>
     <!-- <form @submit.prevent="agregarArea()">
            <div class="input-group mb-3">
                <input type="text" class="form-control" v-model="Departamento.Area" placeholder="Nuevo Departamento"
                    aria-describedby="button-addon2">
                <button class="btn btn-outline-secondary" type="submit">Agregar</button>
            </div>
      </form> -->
      <div class="abs-center" v-show="showy1">

      <div class="accordion accordion-flush" id="accordionFlushExample" >
        <div class="accordion-item">
          <h2 class="accordion-header" id="flush-headingOne">
            <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseOne" aria-expanded="false" aria-controls="flush-collapseOne">
              Nuevo Departamento
            </button>
          </h2>
          <div id="flush-collapseOne" class="accordion-collapse collapse" aria-labelledby="flush-headingOne" data-bs-parent="#accordionFlushExample">
            <div class="accordion-body">
            <form @submit.prevent="agregarArea()">
            <input type="text" class="form-control" v-model="Departamento.Area" placeholder="Nombre Departamento"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Departamento.Encargado" placeholder="Nombre Encargado"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Departamento.Funcionarios" placeholder="Nº de Funcionarios"
                    aria-describedby="button-addon2">
                <button class="btn btn-outline-secondary" type="submit">Agregar</button>
            </form>

            </div>
          </div>
        </div>
      </div>

      </div>
      
    
      <div class="row">
        <div class="col-sm-6" v-for="(value) in Departamentos" :key="value.id">
          <div class="card">
            <div class="card-body">
              <h5 class="card-title">{{value.Area}}</h5>
              <p class="card-text">Encargado : {{value.Encargado}}</p>
              <p class="card-text">Personal : {{value.Funcionarios}}</p>
              <button  @click="IrActivos(value.id,value.Area)">Activos</button>
              <button  @click="EditarArea(value.id)">Editar</button>
            </div>
          </div>
        </div>
      
      </div>
    <div id="EditShow" class="EditShow" v-show="showy2">

    <div class="abs-center2">
            
          <h2 class="EditT" id="TituEdit">Editando : {{Departamento.Area}}</h2>

            <input type="text" class="form-control" v-model="Departamento.Area" placeholder="Nuevo Nombre Departamento"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Departamento.Encargado" placeholder="Nuevo Encargado"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Departamento.Funcionarios" placeholder="Nº de Funcionarios"
                    aria-describedby="button-addon2">
            <div>
                    <button @click="irA('editar')" >Guardar</button> <button @click="irA('eliminar')">Eliminar</button>
                    <button @click="irA('cancelar')">Cancelar</button>
            </div>
    </div>
    </div>

  </div>
</template>

<script>


export default {
  name: 'Homeview',
  data() {
          return{
           Departamento:{
            Area: null,
            Encargado:null,
            Funcionarios: 0
           },
           
            Departamentos: [],
            showy1: true,
            showy2: false,
            TempId: null,
            c2:0,
            c1:0
          }  
  },
   methods:{
            agregarArea(){
                axios({
                method: "post",
                url: "http://localhost:3333"+"/Departamentos",
                data: this.Departamento
            })
                .then(response => {
                    console.log(response);
                    this.Departamento.Area = null;
                    this.Departamento.Encargado = null;
                    this.Departamento.Funcionarios = 0;
                    this.getAreas();
                })
                .catch(e => console.log(e));
            },
            getAreas() {
            axios({
                method: "get",
                url: "http://localhost:3333"+"/Departamentos"
            })
                .then(response => {
                    this.Departamentos = response.data;
                    console.log(response);
                })
                .catch(e => console.log(e));
        },
        EditarArea(id) {
            this.showy1 = false,
            this.showy2 = true
            console.log(id)
            
            this.c2=0;
            for(this.c1 = 0; this.c1<this.Departamentos.length; this.c1++){
              if(id == this.Departamentos[this.c1].id){
                  this.c2=this.c1;
              }
            }
            console.log(this.Departamentos[this.c2])
            console.log(this.c2);

            this.TempId = id;
            this.Departamento = this.Departamentos[this.c2]
        },
        irA(opcion) {

            switch(opcion) {
            case "editar":
               axios({
                method: "patch",
                url: "http://localhost:3333"+"/Departamentos/"+this.TempId,
                data: this.Departamento
            })
            .then(response => {
                alert("El Departamento se edito exitosamene")
                this.Cancelar();
            })
            .catch(e => console.log(e));
              break;
            case "eliminar":
               if (confirm("Esta seguro de eliminar el Departamento")) {
                    axios({
                        method: "delete",
                        url: "http://localhost:3333"+"/Departamentos/" + this.TempId
                    })
                        .then(response => {
                            this.getAreas();
                            console.log(response);
                            this.Cancelar();

                        })
                        .catch(e => console.log(e));
                }
              break;
            case "cancelar":
             this.Cancelar();
              break;
          }
        },
        Cancelar(){
              this.showy1 = true;
              this.showy2 = false;
              this.TempId = null;
              this.Departamento.Area = null;
              this.Departamento.Encargado = null;
              this.Departamento.Funcionarios = 0;
              this.getAreas();
        }
        ,
        IrActivos(id,name){
            this.$router.push({ name: 'verActivos', params: { id: id,depa:name } });
        }
        },
        mounted() {
        this.getAreas()
        }
}

</script>

<style scoped>
.abs-center {
    max-width: 400px;
}

.EditShow{
  position: absolute;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100vw;
  background-color: rgb(17, 17, 56);
  opacity: 0.8;
}

.EditT{
  color: aliceblue;
}
</style>
