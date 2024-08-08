<template>
  <div>
    <h1>Editar Medico</h1>
    <form @submit.prevent="submitForm" v-if="form">
      <div class="form-group">
        <label for="name">Nombre del medico:</label>
        <input type="text" id="name" v-model="form.nombre" :class="{ 'is-invalid': errors.nombre }"
          placeholder="Ingrese el nombre" />
        <div v-if="errors.nombre" class="invalid-feedback">{{ errors.nombre }}</div>
      </div>

      <div class="form-group">
        <label for="especialidad">Especialidad:</label>
        <select id="especialidad" v-model="form.especialidad" :class="{ 'is-invalid': errors.especialidad }">
          <option :value="especialidad" v-for="(especialidad, index) in especialidadList"
            :key="`especialidad-${index}`">{{ especialidad }}</option>
        </select>
        <div v-if="errors.especialidad" class="invalid-feedback">{{ errors.especialidad }}</div>
      </div>

      <div class="form-group">
        <label for="telefono">Telefono:</label>
        <input type="number" id="telefono" v-model="form.telefono" :class="{ 'is-invalid': errors.telefono }"
          placeholder="Ingrese el telefono" />
        <div v-if="errors.telefono" class="invalid-feedback">{{ errors.telefono }}</div>
      </div>

      <button type="submit" class="btn btn-primary">Registrar</button>
    </form>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'MedicoEdit',
  data() {
    return { 
      especialidadList: [
        "Odontologo",
        "Odontopediatra",
        "Ortodoncista",
        "Periodoncista",
        "Endodoncista",
        "Implantología",
        "Cirujano Oral y Maxilofacial"
      ],
      errors: {}
    };
  },
  props: ['item'],
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.nombre) {
        this.errors.nombre = 'El nombre es obligatorio.';
      }

      if (!this.form.especialidad) {
        this.errors.especialidad = 'La especialidad es obligatoria.';
      }

      if (!this.item.telefono) {
        this.errors.telefono = 'El teléfono es obligatorio.';
      } else if (!/^(\+?\d{1,4}[-.\s]?)?(\(?\d{1,4}\)?[-.\s]?)?\d{1,4}([-.\s]?\d{1,9})+$/.test(this.item.telefono)) {
        this.errors.telefono = 'El teléfono no es válido.';
      }

      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) { 
        this.save(); 
        this.form = {
          nombre: '',
          especialidad: '',
          telefono: ''
        };
      }
    },
    save() {
      const vm = this;
      console.log("===============editando medico==========")
      console.log(this.form);
      this.axios.patch(this.baseUrl + "/medicos/" + this.item.id, this.form)
        .then(function (response) {
          console.log(response);
          if (response.status == '200') {
            vm.$emit('on-update', response.data);
          }
          vm.itemList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    }
  },
  computed: { 
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    },
    form() {
      return Object.assign({}, this.item);
    }
  },
  mounted() {
  }
}
</script>

<style scoped></style>