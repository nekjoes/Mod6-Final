<template>
   <div >
    <h4>Activos :{{TituDep}}</h4>

    <div>

         <div class="abs-center" v-show="showy1">

      <div class="accordion accordion-flush" id="accordionFlushExample" >
        <div class="accordion-item">
          <h2 class="accordion-header" id="flush-headingOne">
            <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#flush-collapseOne" aria-expanded="false" aria-controls="flush-collapseOne">
              Nuevo Activo
            </button>
          </h2>
          <div id="flush-collapseOne" class="accordion-collapse collapse" aria-labelledby="flush-headingOne" data-bs-parent="#accordionFlushExample">
            <div class="accordion-body">
            <form @submit.prevent="agregarActivo()">
            <input type="text" class="form-control" v-model="Activo.Tipo" placeholder="Tipo"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Activo.Marca" placeholder="Marca"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Activo.Modelo" placeholder="Modelo"
                    aria-describedby="button-addon2">

            <select class="form-select form-select-sm" aria-label=".form-select-sm example" v-model="Selected">
                <option disabled value="">Please select one</option>
                <option value="Nuevo">Nuevo</option>
                <option value="Usado">Usado</option>
                <option value="DesUso">DesUso</option>
            </select>
                <button class="btn btn-outline-secondary" type="submit">Agregar</button>
            </form>

            </div>
          </div>
        </div>
      </div>

      </div>
      <div>
        <h5>Buscador de Activos {{textoABuscar}}</h5>
        <form action="">
        <div class="input-group mb-3">
            <input type="text" v-model="textoABuscar" class="form-control" placeholder="Buscar activos" >
            <button class="btn btn-outline-secondary" @click.prevent="getActivos2()">Buscar</button>
            <button class="btn btn-outline-secondary" @click="getActivos()">Limpiar</button>
        </div>
       </form> 

      </div>
      <div>
        <h5>Filtrar Activos</h5>
        <form action="">
        <div class="input-group mb-3">
            <select class="form-select form-select-sm" aria-label=".form-select-sm example" v-model="Selected">
                <option disabled value="">Please select one</option>
                <option value="Nuevo">Nuevo</option>
                <option value="Usado">Usado</option>
                <option value="DesUso">DesUso</option>
            </select>
            <button class="btn btn-outline-secondary" @click.prevent="getActivos3()">Filtrar</button>
            <button class="btn btn-outline-secondary" @click="getActivos()">Limpiar</button>
        </div>
       </form> 

      </div>

    </div>
     
         
      
    
      <div class="row">
        <div class="col-sm-6" v-for="(value) in Activos2" :key="value.id">
          <div class="card">
            <div class="card-body">
              <h5 class="card-title">{{value.Tipo}}</h5>
              <p class="card-text">Marca : {{value.Marca}}</p>
              <p class="card-text">Modelo : {{value.Modelo}}</p>
              <p class="card-text">Estado : {{value.Estado}}</p>
              <button  @click="EditarActivos(value.id)">Editar</button>
            </div>
          </div>
        </div>
      
      </div>
    <div id="EditShow" class="EditShow" v-show="showy2">

    <div class="abs-center2">
            
          <h2 class="EditT" id="TituEdit">Editando : {{Activo.Tipo}}</h2>

            <input type="text" class="form-control" v-model="Activo.Tipo" placeholder="Nuevo Tipo"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Activo.Marca" placeholder="Marca"
                    aria-describedby="button-addon2">
              <input type="text" class="form-control" v-model="Activo.Modelo" placeholder="Modelo"
                    aria-describedby="button-addon2">
              <select class="form-select form-select-sm" aria-label=".form-select-sm example" v-model="Selected">
                <option disabled value="">Please select one</option>
                <option value="Nuevo">Nuevo</option>
                <option value="Usado">Usado</option>
                <option value="DesUso">DesUso</option>
            </select>
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
           Activo:{
            Tipo: null,
            Marca:null,
            Modelo:null,
            Estado:null,
            AreaId:null
           },
           
            Activos: [],
            Activos2: [],
            Activos3: [],
            showy1: true,
            showy2: false,
            TempId: null,
            TituDep:this.$route.params.depa,
            c2:0,
            c1:0,
            c3:0,
            c4:0,
            Selected:'',
            textoABuscar: ''
          }  
  },
   methods:{

            agregarActivo(){
                this.Activo.AreaId = this.$route.params.depa;
                if(this.Activo.Estado == null){
                    this.Activo.Estado = "Nuevo";
                }else{
                    this.Activo.Estado = this.Selected;
                }

                axios({
                method: "post",
                url: "http://localhost:3333"+"/Activos",
                data: this.Activo
            })
                .then(response => {
                    console.log(response);
                    this.Activo.Tipo = null;
                    this.Activo.Marca = null;
                    this.Activo.Modelo = null;
                    this.Activo.Estado = null;
                    this.getActivos();
                })
                .catch(e => console.log(e));
            },
            getActivos() {
            axios({
                method: "get",
                // url: "http://localhost:3333"+"/Activos"
                url: "http://localhost:3333"+"/Activos/?q="+this.textoABuscar
            })
                .then(response => {
                    this.Activos2 = [];
                    this.Activos = response.data;
                     this.c2=0;
                    for(this.c1 = 0; this.c1<this.Activos.length; this.c1++){
                        if(this.$route.params.depa == this.Activos[this.c1].AreaId){
                        this.Activos2.push(this.Activos[this.c1])
                        }
                    }
                    
                    console.log(this.Activos2.length);
                })
                .catch(e => console.log(e));
        },

        getActivos2() {
            axios({
                method: "get",
                url: "http://localhost:3333"+"/Activos"
                //url: "http://localhost:3333"+"/Activos/?q="+this.textoABuscar
            })
                .then(response => {
                    this.Activos2 = [];
                    this.Activos = response.data;
                     this.c2=0;
                    for(this.c1 = 0; this.c1<this.Activos.length; this.c1++){
                        if(this.$route.params.depa == this.Activos[this.c1].AreaId){
                        this.Activos2.push(this.Activos[this.c1])
                        }
                    }
                    

                    this.Activos3 = [];
                    //this.Activos = response.data;
                     //this.c4=0;
                    for(this.c3 = 0; this.c3<this.Activos2.length; this.c3++){
                        if(this.textoABuscar == this.Activos2[this.c3].Tipo){
                        this.Activos3.push(this.Activos2[this.c3])
                        }
                    }
                    this.Activos2 = this.Activos3;
                    this.textoABuscar = "";
                    console.log(this.Activos3.length);
                })
                .catch(e => console.log(e));
        },
        getActivos3() {
            axios({
                method: "get",
                url: "http://localhost:3333"+"/Activos"
                //url: "http://localhost:3333"+"/Activos/?q="+this.textoABuscar
            })
                .then(response => {
                    this.Activos2 = [];
                    this.Activos = response.data;
                     this.c2=0;
                    for(this.c1 = 0; this.c1<this.Activos.length; this.c1++){
                        if(this.$route.params.depa == this.Activos[this.c1].AreaId){
                        this.Activos2.push(this.Activos[this.c1])
                        }
                    }
                    

                    this.Activos3 = [];
                    //this.Activos = response.data;
                     //this.c4=0;
                    for(this.c3 = 0; this.c3<this.Activos2.length; this.c3++){
                        if(this.Selected == this.Activos2[this.c3].Estado){
                        this.Activos3.push(this.Activos2[this.c3])
                        }
                    }
                    this.Activos2 = this.Activos3;
                    this.Selected = "";
                    console.log(this.Activos3.length);
                })
                .catch(e => console.log(e));
        },
        EditarActivos(id) {
            this.showy1 = false,
            this.showy2 = true
            console.log(id)
            
            this.c2=0;
            for(this.c1 = 0; this.c1<this.Activos2.length; this.c1++){
              if(id == this.Activos2[this.c1].id){
                  this.c2=this.c1;
              }
            }
            console.log(this.Activos[this.c2])
            console.log(this.c2);

            this.TempId = id;
            this.Activo = this.Activos2[this.c2]
        },
        irA(opcion) {

            switch(opcion) {
            case "editar":
                   this.Activo.AreaId = this.$route.params.depa;
                if(this.Activo.Estado == null){
                    this.Activo.Estado = "Nuevo";
                }else{
                    this.Activo.Estado = this.Selected;
                }
               axios({
                method: "patch",
                url: "http://localhost:3333"+"/Activos/"+this.TempId,
                data: this.Activo
            })
            .then(response => {
                alert("El Activo se edito exitosamene")
                this.Cancelar();
            })
            .catch(e => console.log(e));
              break;
            case "eliminar":
               if (confirm("Esta seguro de eliminar el Activo")) {
                    axios({
                        method: "delete",
                        url: "http://localhost:3333"+"/Activos/" + this.TempId
                    })
                        .then(response => {
                            this.getActivos();
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
              this.Activo.Tipo = null;
              this.Activo.Marca = null;
              this.Activo.Modelo = null;
              this.Activo.Estado = null;
              this.Activo.AreaId = null;
              this.Selected = "";
              this.getActivos();
        }
        },
        mounted() {
        this.getActivos()
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
  z-index: 5;
}

.EditT{
  color: aliceblue;
}
</style>
