<template>
  <div class="py-5">
    <div class="bg-green-darken-4 d-inline-block rounded-t pa-2 text-body-2">
      Seccion {{ id + 1 }} de {{ cantidad }}
    </div>
    <div class="bg-white pa-3 rounded-b-xl">
      <v-text-field
        v-if="formData.type.toLowerCase() === 'input'"
        :label="formData.question"
        required
        v-model="respuesta"
        :rules="rules"
      ></v-text-field>

      <v-select
        v-else-if="formData.type.toLowerCase() === 'select'"
        :items="formData.items"
        :item-title="(item) => JSON.stringify(item.value)"
        item-value="value"
        :label="formData.question"
        v-model="respuesta"
        :rules="rules"
      >
      </v-select>

      <template v-if="formData.type.toLowerCase() === 'checkbox'">
        <p>{{ formData.question }}</p>
        <v-checkbox
          v-for="item in formData.items"
          :label="item"
          :value="item"
          color="green-darken-4"
          v-model="respuestaCheck"
          :rules="[(v) => v.length > 0 || 'Debe elegir al menos 1 opcion']"
        ></v-checkbox>
      </template>
    </div>
  </div>
</template>

<script setup>
  const rules = ref([
    (value) => {
      if (value) return true;

      if (props.formData.type.toLowerCase() === 'input') {
        return 'Este campo es requerido';
      } else if (props.formData.type.toLowerCase() === 'checkbox') {
        return 'debe elegir al menos 1 opcion';
      } else if (props.formData.type.toLowerCase() === 'select') {
        return 'debe elegir al menos 1 opcion';
      }
    },
  ]);
  const respuesta = ref();
  const respuestaCheck = ref([]);
  const id = ref(props.id);
  const pregunta = ref(props.formData.question);
  const props = defineProps({
    formData: {
      type: Object,
    },
    id: Number,
    cantidad: Number,
  });

  const emit = defineEmits(['update:respuesta']);

  watch([respuesta, respuestaCheck], () => {
    if (props.formData.type.toLowerCase() === 'checkbox') {
      emit('update:respuesta', { id, pregunta, respuestaCheck });
    } else {
      emit('update:respuesta', { id, pregunta, respuesta });
    }
  });
</script>

<style scoped></style>
