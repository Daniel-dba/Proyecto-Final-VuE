<template>
    <div class="container">
        <h5>Activos</h5>
    
        <div class="card">
            <div class="card-content">
                <form @submit.prevent="nuevo()" >
                    <h5>Nuevo Activo</h5>
                    <p>Tipo de Activo: <input type="text" v-model="payload.tipo" required/></p>
                    <p>Marca: <input type="text" v-model="payload.marca" required/></p>
                    <p>Modelo: <input type="text" v-model="payload.modelo" required/></p>
                    <p v-if="activo.length > 0">
                        <select v-model="payload.areaId">
                            <option :value="area.id" v-for="area in areas">{{activo.tipo}}</option>
                        </select>
                    </p>
                    <button type="submit" class="waves-effect waves-light btn-small">Agregar</button>
                </form>
            </div>
        </div>
    
        <div class="card">
                <div class="card-content">
                    <form @submit.prevent="getList()">
                        <h5>Buscar activo</h5>
                        <p>Nombre Activo: <input type="search" v-model="search" @search="getList()" /></p>
                        <button type="submit" class="waves-effect waves-light btn-small">buscar</button>
                    </form>
                </div>
            </div>
    
        <div class="card">
                <div class="card-content">
                    <h5>filtro</h5>
                    <div class="input-field ">
                        <select @change="filter('areaId',$event.target.value)">
                        <option value="" selected>todos</option>
                        <option value="true">nuevo</option>
                        <option value="true">usado</option>
                        <option value="false">desuso</option>
                            <option :value="activo.id" v-for="activo in activos">{{activo.tipo}}</option>
                        </select>
                        <label>Materialize Select</label>
                    </div>
        
                </div>
            </div>
    
        <div class="card">
            <div class="card-content">
                <table>
                    <thead>
                        <tr>
                            <th>Tipo</th>
                            <th>Marca</th>
                            <th>Modelo</th>
                            <th>AreaId</th>
                            <th></th>
                        </tr>
                    </thead>
    
                    <tbody>
                        <tr v-for="item in items">
                            <td>{{item.tipo}}</td>
                            <td>{{item.marca}}</td>
                            <td>{{item.modelo}}</td>
                            <td>{{item.area.id}}</td>
                            <td>{{item.active ? "nuevo":"usado"}}</td>
                            <td>
                                <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light red"><i class="material-icons" @click="eliminar(item.id)" >delete</i></a>
                                <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light blue "><i class="material-icons" @click="update(item.id)" >edit</i></a>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    tipo: 'ActivoView',
    data() {
        const api = process.env.VUE_APP_API;
        return {
            api,
            items: [],
            search: '',
            toFilter: '',
            categories: [],
            payload: {
                // name: null,
                // price: null,
                // categoryId: null
                tipo: null,
                marca: null,
                modelo: null,
                areaId: null,
                active: true
            }
        }
    },
    methods: {
        filter(tipo, value) {
            this.toFilter = value === '' ? '' : '&' + tipo + '=' + value;
            this.getList();
        },
        update(id) {
            this.$router.push('/activo/' + id);
        },
        eliminar(id) {
            if (confirm("Esta seguro de eliminar?.")) {
                this.axios({
                    method: 'delete',
                    url: this.api + '/activo/' + id
                }).
                then((response) => {
                    this.getList();
                }).
                catch((error) => {
                    console.log(error);
                });
            }
        },
        nuevo() {
            this.axios({
                method: 'post',
                url: this.api + '/activo',
                data: this.payload
            }).
            then((response) => {
                this.getList();
                console.log(response);
            }).
            catch((error) => {
                console.log(error);
            });
        },
        getList(tipo, value) {

            this.axios({
                method: 'get',
                url: this.api + '/activos?_expand=area&q=' + this.search + this.toFilter
            }).
            then((response) => {
                this.items = response.data;
            }).
            catch((error) => {
                console.log(error);
            })
        },
        getAreaList() {

            this.axios({
                method: 'get',
                url: this.api + '/areas'
            }).
            then((response) => {
                this.areas = response.data;
                const intervalo = setTimeout(() => {
                    this.intSelect(); 
                    clearTimeout(intervalo);
                }, 3000);
                
            }).
            catch((error) => {
                console.log(error);
            })
        },
        intSelect(){
            this.getList();
            this.getAreaList();
            var elems = document.querySelectorAll('select');
            var instances = M.FormSelect.init(elems);
        }
    },
    components: {

    },
    mounted() {
        this.getList();
        this.getAreaList();
        var elems = document.querySelectorAll('select');
        var instances = M.FormSelect.init(elems);
    }
}
</script>
