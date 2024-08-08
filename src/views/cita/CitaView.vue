<template>
    <div>
        <Modal v-model:modelValue="showModalNuevo">
            <CitaNewView @on-register="onRegister()" />
        </Modal>
        <Modal v-model:modelValue="showModalEdit">
            <CitaEditView @on-update="onUpdate()" :item="itemToEdit" />
        </Modal>
        <h1>Lista de Citas</h1>
        <button @click="showModalNuevo = true" class="btn btn-primary">Nuevo</button>
        <button @click="buscar()" class="btn btn-lith" style="float:right">Buscar</button>
        <input type="search" style="float:right" v-model="textToSearch" @search="buscar()">
        <div style="margin: 20px 0;">
            <h3>Filtros:</h3>
            <form @submit.prevent="filtrar()">

                <label for="costo"> Costo >= a: </label>
                <input type="number" id="costo" v-model="filter.costo" placeholder="Costo mayor o igual a" />

                <label for="fecha"> Fecha: </label>
                <input type="date" id="fecha" v-model="filter.fecha" placeholder="Ingrese la fecha" />

                <label for="medico"> Medico: </label>
                <select id="medico" v-model="filter.medicoId">
                    <option value="">Todos</option>
                    <option :value="medico.id" v-for="(medico, index) in medicoList" :key="`medico-${index}`">{{
                        medico.nombre }}
                    </option>
                </select>
                <button type="submit" class="btn btn-lith">Fitrar</button>
            </form>
        </div>
        <table>
            <thead>
                <tr>
                    <th>No.</th>
                    <th>Fecha</th>
                    <th>Hora</th>
                    <th>Medico</th>
                    <th>Paciente</th>
                    <th>Solicitud</th>
                    <th>Costo</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in itemList" :key="index">
                    <td>{{ 1 + index }}</td>
                    <td>{{ item.fecha }}</td>
                    <td>{{ item.hora }}</td>
                    <td>{{ item.medico.nombre }}</td>
                    <td>{{ item.paciente.nombre }}</td>
                    <td>{{ item.solicitud }}</td>
                    <td>{{ item.costo }}</td>
                    <td>
                        <button @click="edit(item)" class="btn btn-dark" style="margin-right: 15px;">Editar</button>
                        <button @click="Eliminar(item.id)" class="btn btn-danger">Eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'
import Modal from '../../components/Modal.vue'
import CitaNewView from './CitaNewView.vue'
import CitaEditView from './CitaEditView.vue'


export default {
    name: 'Cita',
    data() {
        return {
            currentPage: 1,
            totalPages: 50,
            showModalNuevo: false,
            showModalEdit: false,
            itemToEdit: null,
            textToSearch: '',
            textToFilter: '',
            itemList: [],
            medicoList: [],
            path: '',
            filter: {
                fecha: null,
                medicoId: '',
                costo: null,
            }
        }
    },
    components: {
        Modal,
        CitaNewView,
        CitaEditView
    },
    methods: {

        ...mapActions(['increment']),
        getList() {
            const vm = this;
            this.path = this.baseUrl + "/citas?_sort=fecha,hora&_order=asc,asc&_expand=paciente&_expand=medico" + this.textToFilter + "&q=" + this.textToSearch;
            console.log(this.path);
            this.axios.get(this.path)
                .then(function (response) {
                    console.log(response); 
                    let data = response.data;

                    if (vm.filter.costo != null && vm.filter.costo != '') {
                        data = data.filter(item => item.costo >= vm.filter.costo);
                    }
                    vm.itemList = data;

                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        getMedicoList() {
            const vm = this;
            this.axios.get(this.baseUrl + "/medicos")
                .then(function (response) {
                    vm.medicoList = response.data;
                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        edit(item) {
            this.itemToEdit = Object.assign({}, item);
            this.showModalEdit = true;
        },
        Eliminar(id) {
            if (confirm("¿Esta Seguro de eliminar el registro?")) {
                const vm = this;
                this.axios.delete(this.baseUrl + "/citas/" + id)
                    .then(function (response) {
                        vm.getList();
                        vm.$toast.show("Registro eliminado.", "danger");
                    })
                    .catch(function (error) {
                        console.error(error);
                    });
            }

        },
        buscar() {
            this.getList();
        },
        filtrar() {
            this.textToFilter = '';
            if (this.filter.fecha != null && this.filter.fecha != '') {
                this.textToFilter += '&fecha=' + this.filter.fecha;
            }
            if (this.filter.medicoId != null && this.filter.medicoId != '') {
                this.textToFilter += '&medicoId=' + this.filter.medicoId;
            }
            this.getList();
        },
        onRegister(event) {
            this.getList();
            this.showModalNuevo = false;
            this.$toast.show('Registro exitoso', 'success');
        },
        onUpdate(event) {
            this.getList();
            this.showModalEdit = false;
            this.itemToEdit = null;
            this.$toast.show('Edicion exitosa', 'info');
        },
        showToast(message, type) {
            this.$toast.show(message, type);
        }
    },
    computed: {
        // propiedades computadas que dependen de otras propiedades reactivas
        ...mapState(['count']),
        ...mapGetters(['doubleCount', 'getBaseUrl']),
        baseUrl() {
            return this.getBaseUrl
        }
    },
    props: {
        // propiedades que el componente puede recibir.
    },
    mounted() {
        this.getList();
        this.getMedicoList();
    },
    emits: [] // los eventos personalizados que el componente puede emitir.
}
</script>

<style></style>