<template>
  <v-sheet
    width="500"
    class="mx-auto elevation-1 pa-10 rounded-xl bg-light-green-lighten-5 text-green-darken-4 mt-5"
  >
    <template v-if="!complete">
      <div v-for="{ ...entry } in entries">
        <p class="text-h5 text-center">{{ entry.name }}</p>
        <p class="text-h6">{{ entry.descripcion }}</p>
        <v-form validate-on="submit lazy" @submit.prevent="prueba">
          <Input
            v-for="(items, index) in entry.form"
            :form-data="items"
            :id="index"
            :cantidad="entry.form.length"
            @update:respuesta="(e) => updateArray(e)"
            :key="index"
          />

          <v-btn
            type="submit"
            block
            class="mt-2 rounded bg-green-darken-4 text-white"
            :loading="loading"
            >Confirmar</v-btn
          >
        </v-form>
      </div>
    </template>

    <template v-if="complete">
      <div transition="fade-transition">
        <p class="text-h5 text-center mb-10">Tus Respuestas</p>
        <div
          v-for="({ pregunta, respuesta, respuestaCheck }, index) in form1"
          :key="index"
          class="my-5"
        >
          <div class="bg-green-darken-4 d-inline-block rounded-t pa-2 text-body-2">
            {{ pregunta }}
          </div>
          <div class="bg-white pa-3 rounded-b-lg">
            {{ respuesta || respuestaCheck.join(',') }}
          </div>
        </div>
        <v-btn
          type="submit"
          block
          class="mt-10 rounded bg-green-darken-4 text-white"
          @click="complete = !complete"
          >Volver a formulario</v-btn
        >
      </div>
    </template>
  </v-sheet>
</template>
<script setup>
  const form1 = ref([]);
  const complete = ref(false);
  const loading = ref(false);

  const { data: formulario } = await useFetch(
    'https://run.mocky.io/v3/6bd01347-72e9-49da-809a-d5002ca63b2c'
  );
  const entries = [];
  for (let id of Object.keys(formulario.value)) {
    entries.push({
      id,
      ...formulario.value[id],
    });
  }

  const prueba = async (event) => {
    loading.value = true;
    const { valid } = await event;

    if (valid) {
      await new Promise((resolve) => {
        setTimeout(() => {
          complete.value = true;
          resolve(true);
        }, 1000);
      });
    }
    loading.value = false;
  };

  const updateArray = (e) => {
    const idx = form1.value.map((item) => item.id).indexOf(e.id.value);
    if (idx >= 0) {
      form1.value[idx] = e;
    } else {
      form1.value.push(e);
    }
  };
</script>
