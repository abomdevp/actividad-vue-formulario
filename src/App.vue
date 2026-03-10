<script setup>
import { reactive, computed, ref } from 'vue'

const niveles = [
  { id: 1, label: 'Junior', value: 'junior' },
  { id: 2, label: 'Semi Senior', value: 'semi senior' },
  { id: 3, label: 'Senior', value: 'senior' }
]

const interesesDisponibles = [
  { id: 1, label: 'Frontend', value: 'frontend' },
  { id: 2, label: 'Backend', value: 'backend' },
  { id: 3, label: 'UI/UX', value: 'ui/ux' },
  { id: 4, label: 'DevOps', value: 'devops' },
  { id: 5, label: 'Data', value: 'data' }
]

const paises = [
  { code: 'CL', name: 'Chile' },
  { code: 'AR', name: 'Argentina' },
  { code: 'PE', name: 'Perú' },
  { code: 'MX', name: 'México' },
  { code: 'CO', name: 'Colombia' }
]

const tecnologiasDisponibles = [
  { id: 1, label: 'Vue', value: 'Vue' },
  { id: 2, label: 'React', value: 'React' },
  { id: 3, label: 'Angular', value: 'Angular' },
  { id: 4, label: 'Svelte', value: 'Svelte' },
  { id: 5, label: 'Nuxt', value: 'Nuxt' }
]

const form = reactive({
  nombre: '',
  edad: null,
  biografia: '',
  nivel: 'junior',
  intereses: [],
  pais: null,
  tecnologias: []
})

const bioDraft = ref('')
const enviado = ref(false)

const nombreValido = computed(() => form.nombre.trim().length > 0)

const edadValida = computed(() => {
  return (
    typeof form.edad === 'number' &&
    !Number.isNaN(form.edad) &&
    form.edad >= 0 &&
    form.edad <= 120
  )
})

const interesesValidos = computed(() => form.intereses.length >= 1)

const formValido = computed(() => {
  return nombreValido.value && edadValida.value && interesesValidos.value
})

const payloadPreview = computed(() =>
  JSON.stringify(
    {
      nombre: form.nombre,
      edad: form.edad,
      biografia: form.biografia,
      nivel: form.nivel,
      intereses: form.intereses,
      pais: form.pais,
      tecnologias: form.tecnologias
    },
    null,
    2
  )
)

function handleSubmit() {
  enviado.value = true

  if (!formValido.value) return

  const payload = {
    nombre: form.nombre,
    edad: form.edad,
    biografia: form.biografia,
    nivel: form.nivel,
    intereses: form.intereses,
    pais: form.pais,
    tecnologias: form.tecnologias
  }

  console.log('Payload del formulario:', payload)
  alert('Formulario enviado correctamente. Revisa la consola para ver el payload JSON.')
}
</script>

<template>
  <div class="page">
    <div class="container">
      <form class="card" @submit.prevent="handleSubmit">
        <h1>Registro de perfil</h1>
        <p class="subtitle">
          Demo de two-way binding con <code>v-model</code>, <code>:value</code> y validación básica.
        </p>

        <section class="section">
          <h2>Datos básicos</h2>

          <div class="field">
            <label for="nombre">Nombre</label>
            <input
              id="nombre"
              type="text"
              v-model.trim="form.nombre"
              placeholder="Ej: Fran Pérez"
              :class="{ invalid: enviado && !nombreValido }"
            />
            <small v-if="enviado && !nombreValido" class="error">
              El nombre es obligatorio.
            </small>
          </div>

          <div class="field">
            <label for="edad">Edad</label>
            <input
              id="edad"
              type="number"
              v-model.number="form.edad"
              min="0"
              max="120"
              placeholder="Ej: 25"
              :class="{ invalid: enviado && !edadValida }"
            />
            <small v-if="enviado && !edadValida" class="error">
              La edad debe estar entre 0 y 120.
            </small>
          </div>

          <div class="field">
            <label for="bio">Biografía</label>
            <textarea
              id="bio"
              rows="5"
              v-model.lazy="form.biografia"
              @input="bioDraft = $event.target.value"
              placeholder="Cuéntanos algo sobre ti"
            ></textarea>
            <small class="hint">
              Caracteres: {{ bioDraft.length }}
              <span class="muted">(.lazy actualiza el valor guardado al salir del campo)</span>
            </small>
          </div>
        </section>

        <section class="section">
          <h2>Preferencias</h2>

          <div class="field">
            <label>Nivel</label>
            <div class="choices-column">
              <label
                v-for="nivel in niveles"
                :key="nivel.id"
                class="choice-row"
              >
                <input
                  type="radio"
                  name="nivel"
                  v-model="form.nivel"
                  :value="nivel.value"
                />
                <span>{{ nivel.label }}</span>
              </label>
            </div>
          </div>

          <div class="field">
            <label>Intereses</label>
            <div class="choices-grid">
              <label
                v-for="interes in interesesDisponibles"
                :key="interes.id"
                class="choice-box"
              >
                <input
                  type="checkbox"
                  v-model="form.intereses"
                  :value="interes.value"
                />
                <span>{{ interes.label }}</span>
              </label>
            </div>
            <small v-if="enviado && !interesesValidos" class="error">
              Debes seleccionar al menos un interés.
            </small>
          </div>

          <div class="field">
            <label for="pais">País</label>
            <select id="pais" v-model="form.pais">
              <option :value="null">Selecciona un país</option>
              <option
                v-for="pais in paises"
                :key="pais.code"
                :value="pais"
              >
                {{ pais.name }}
              </option>
            </select>
            <small class="hint">
              Aquí se guarda un objeto completo con <code>:value="pais"</code>.
            </small>
          </div>

          <div class="field">
            <label for="tecnologias">Tecnologías</label>
            <select id="tecnologias" v-model="form.tecnologias" multiple size="5">
              <option
                v-for="tech in tecnologiasDisponibles"
                :key="tech.id"
                :value="tech.value"
              >
                {{ tech.label }}
              </option>
            </select>
            <small class="hint">
              Mantén presionada <strong>Ctrl</strong> para seleccionar varias opciones.
            </small>
          </div>
        </section>

        <section class="section">
          <h2>Estado de validación</h2>
          <ul class="status-list">
            <li :class="nombreValido ? 'ok' : 'bad'">
              Nombre válido: {{ nombreValido ? 'Sí' : 'No' }}
            </li>
            <li :class="edadValida ? 'ok' : 'bad'">
              Edad válida: {{ edadValida ? 'Sí' : 'No' }}
            </li>
            <li :class="interesesValidos ? 'ok' : 'bad'">
              Intereses válidos: {{ interesesValidos ? 'Sí' : 'No' }}
            </li>
          </ul>
        </section>

        <button class="submit-btn" type="submit" :disabled="!formValido">
          Enviar formulario
        </button>
      </form>

      <aside class="card">
        <h2>Resumen en tiempo real</h2>

        <div class="preview-list">
          <div class="preview-item">
            <span class="preview-label">Nombre</span>
            <span>{{ form.nombre || '—' }}</span>
          </div>

          <div class="preview-item">
            <span class="preview-label">Edad</span>
            <span>{{ form.edad ?? '—' }}</span>
          </div>

          <div class="preview-item">
            <span class="preview-label">Biografía</span>
            <span>{{ form.biografia || '—' }}</span>
          </div>

          <div class="preview-item">
            <span class="preview-label">Nivel</span>
            <span>{{ form.nivel || '—' }}</span>
          </div>

          <div class="preview-item">
            <span class="preview-label">Intereses</span>
            <span>{{ form.intereses.length ? form.intereses.join(', ') : '—' }}</span>
          </div>

          <div class="preview-item">
            <span class="preview-label">País</span>
            <span>{{ form.pais ? `${form.pais.name} (${form.pais.code})` : '—' }}</span>
          </div>

          <div class="preview-item">
            <span class="preview-label">Tecnologías</span>
            <span>{{ form.tecnologias.length ? form.tecnologias.join(', ') : '—' }}</span>
          </div>
        </div>

        <h3 class="json-title">Payload JSON</h3>
        <pre>{{ payloadPreview }}</pre>
      </aside>
    </div>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

.page {
  min-height: 100vh;
  padding: 32px 16px;
  background: linear-gradient(135deg, #eef2ff 0%, #f8fafc 100%);
  color: #1f2937;
  font-family: Inter, Arial, Helvetica, sans-serif;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 1.1fr 0.9fr;
  gap: 24px;
  align-items: start;
}

.card {
  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 20px;
  padding: 28px;
  box-shadow: 0 16px 40px rgba(15, 23, 42, 0.08);
}

h1 {
  margin: 0 0 10px;
  font-size: 2rem;
  color: #0f172a;
}

h2 {
  margin: 0 0 16px;
  font-size: 1.25rem;
  color: #111827;
}

h3 {
  margin: 24px 0 12px;
  color: #111827;
}

.subtitle {
  margin: 0 0 24px;
  color: #475569;
  line-height: 1.5;
}

.section + .section {
  margin-top: 28px;
  padding-top: 24px;
  border-top: 1px solid #e5e7eb;
}

.field {
  margin-bottom: 18px;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: 700;
  color: #1f2937;
}

input,
textarea,
select,
button {
  font: inherit;
}

input[type='text'],
input[type='number'],
textarea,
select {
  width: 100%;
  border: 1px solid #cbd5e1;
  border-radius: 12px;
  padding: 12px 14px;
  background: #fff;
  color: #111827;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

input[type='text']:focus,
input[type='number']:focus,
textarea:focus,
select:focus {
  outline: none;
  border-color: #4f46e5;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}

textarea {
  resize: vertical;
  min-height: 120px;
}

.invalid {
  border-color: #dc2626 !important;
  background: #fef2f2;
}

.error {
  display: block;
  margin-top: 8px;
  color: #dc2626;
  font-size: 0.92rem;
}

.hint {
  display: block;
  margin-top: 8px;
  font-size: 0.92rem;
  color: #475569;
}

.muted {
  color: #64748b;
}

.choices-column {
  display: grid;
  gap: 10px;
}

.choice-row {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 12px;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  background: #f8fafc;
  font-weight: 500;
}

.choice-row input {
  width: auto;
  margin: 0;
}

.choices-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
}

.choice-box {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  background: #f8fafc;
  font-weight: 500;
}

.choice-box input {
  width: auto;
  margin: 0;
}

.status-list {
  margin: 0;
  padding-left: 18px;
}

.status-list li {
  margin-bottom: 8px;
}

.ok {
  color: #15803d;
  font-weight: 700;
}

.bad {
  color: #b91c1c;
  font-weight: 700;
}

.submit-btn {
  width: 100%;
  margin-top: 8px;
  padding: 14px 18px;
  border: none;
  border-radius: 14px;
  background: #4f46e5;
  color: white;
  font-weight: 700;
  cursor: pointer;
  transition: transform 0.15s ease, opacity 0.15s ease;
}

.submit-btn:hover:not(:disabled) {
  transform: translateY(-1px);
}

.submit-btn:disabled {
  opacity: 0.55;
  cursor: not-allowed;
}

.preview-list {
  display: grid;
  gap: 12px;
}

.preview-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 12px 14px;
  background: #f8fafc;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
}

.preview-label {
  font-size: 0.9rem;
  font-weight: 700;
  color: #475569;
}

.json-title {
  margin-top: 24px;
}

pre {
  margin: 0;
  padding: 16px;
  border-radius: 14px;
  background: #0f172a;
  color: #e2e8f0;
  overflow: auto;
  font-size: 0.92rem;
  line-height: 1.5;
}

code {
  background: #eef2ff;
  color: #3730a3;
  padding: 2px 6px;
  border-radius: 6px;
}

@media (max-width: 900px) {
  .container {
    grid-template-columns: 1fr;
  }

  .choices-grid {
    grid-template-columns: 1fr;
  }

  h1 {
    font-size: 1.7rem;
  }
}
</style>