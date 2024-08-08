<template>
  <div>
    <h1>Registrar Medico</h1>
    <form @submit.prevent="submitForm">

      <div class="form-group">
        <label for="name">Nombre medico:</label>
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
          placeholder="Ingrese la telefono" />
        <div v-if="errors.telefono" class="invalid-feedback">{{ errors.telefono }}</div>
      </div>
      <button type="submit" class="btn btn-primary">Registrar</button>
    </form>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'
export default {
  name: 'MedicoNew',
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
      form: {
        nombre: '',
        especialidad: '',
        telefono: '', 
      },
      errors: {}
    };
  },
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

      if (!this.form.telefono) {
        this.errors.telefono = 'El telefono es obligatorio.';
      }

      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) {
        // Enviar los datos al servidor
        this.save();
        // Reiniciar el formulario
        this.form = {
          nombre: '',
          especialidad: '',
          telefono: ''
        };
      }
    },
    save() {
      const vm = this;
      this.axios.post(this.baseUrl + "/medicos", this.form)
        .then(function (response) {
          if (response.status == '201') {
            vm.$emit('on-register', response.data);
          }
          vm.itemList = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    },
  },
  computed: {
    // propiedades computadas que dependen de otras propiedades reactivas
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    }
  },
  mounted() {
  },
}
</script>

<style scoped></style>