<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Malla Curricular Interactiva</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <h1>Malla Curricular</h1>
  <div id="malla" class="grid"></div>
  <script src="script.js"></script>
</body>
</html>
body {
  font-family: 'Segoe UI', sans-serif;
  background-color: #e3f2fd;
  color: #0d47a1;
  padding: 20px;
}

h1 {
  text-align: center;
  color: #0d47a1;
}

.grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
  max-width: 1200px;
  margin: 0 auto;
}

.ramo {
  background-color: #bbdefb;
  border: 2px solid #64b5f6;
  padding: 15px;
  border-radius: 12px;
  box-shadow: 2px 2px 5px rgba(0,0,0,0.1);
  position: relative;
  transition: all 0.3s;
}

.ramo.bloqueado {
  background-color: #eeeeee;
  border-color: #bdbdbd;
  color: #9e9e9e;
}

.ramo h3 {
  margin: 0 0 8px;
  font-size: 1rem;
}

button {
  padding: 6px 10px;
  border: none;
  border-radius: 6px;
  background-color: #0288d1;
  color: white;
  cursor: pointer;
  font-size: 0.9rem;
  margin-top: 8px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #01579b;
}

button:disabled {
  background-color: #b0bec5;
  cursor: not-allowed;
}
// Define los ramos, créditos y requisitos
const cursos = [
  { id: "mat1", nombre: "Matemáticas I", creditos: 6, desbloquea: ["mat2", "macro1"] },
  { id: "prog", nombre: "Programación", creditos: 6, desbloquea: ["estad1", "tecno"] },
  { id: "fund1", nombre: "Fund. Admin. y Negocios I", creditos: 6, desbloquea: ["fund2"] },
  { id: "com1", nombre: "Taller de Comunicación I", creditos: 6, desbloquea: ["com2"] },
  { id: "ing1", nombre: "Inglés I", creditos: 5, desbloquea: ["ing2"] },
  { id: "mat2", nombre: "Matemáticas II", creditos: 6, desbloquea: ["mat3", "estad1", "micro2"] },
  { id: "micro1", nombre: "Microeconomía I", creditos: 6, desbloquea: ["macro1", "micro2"] },
  { id: "cont1", nombre: "Contabilidad I", creditos: 6, desbloquea: ["cont2"] },
  { id: "fund2", nombre: "Fund. Admin. y Negocios II", creditos: 6, desbloquea: ["personas", "liderazgo"] },
  { id: "ing2", nombre: "Inglés II", creditos: 5, desbloquea: ["ing3"] },
  { id: "mat3", nombre: "Matemáticas III", creditos: 6, desbloquea: ["econo1", "macro2"] },
  { id: "estad1", nombre: "Estadística I", creditos: 6, desbloquea: ["estad2"] },
  { id: "macro1", nombre: "Macroeconomía I", creditos: 6, desbloquea: ["macro2"] },
  { id: "tecno", nombre: "Tecnología y Empresa", creditos: 6, desbloquea: ["cont2", "estrategia", "datos"] },
  { id: "com2", nombre: "Taller de Comunicación II", creditos: 3, desbloquea: ["liderazgo"] },
  { id: "ing3", nombre: "Inglés III", creditos: 5, desbloquea: ["ing4"] },
  { id: "estad2", nombre: "Estadística II", creditos: 6, desbloquea: ["econo1", "finanzas1", "global", "ops", "marketing"] },
  { id: "micro2", nombre: "Microeconomía II", creditos: 6, desbloquea: [] },
  { id: "cont2", nombre: "Contabilidad II", creditos: 6, desbloquea: ["finanzas1"] },
  { id: "personas", nombre: "Personas y Equipos", creditos: 6, desbloquea: ["estrategia", "etica"] },
  { id: "liderazgo", nombre: "Taller Liderazgo y Trabajo en Equipo", creditos: 3, desbloquea: ["etica"] },
  { id: "ing4", nombre: "Inglés IV", creditos: 5, desbloquea: ["ing5"] },
  { id: "econo1", nombre: "Econometría I", creditos: 6, desbloquea: ["datos"] },
  { id: "macro2", nombre: "Macroeconomía II", creditos: 6, desbloquea: ["global"] },
  { id: "finanzas1", nombre: "Finanzas I", creditos: 6, desbloquea: [] },
  { id: "estrategia", nombre: "Estrategia", creditos: 6, desbloquea: ["global", "ops", "marketing"] },
  { id: "etica", nombre: "Taller de Ética en la Toma de Decisiones", creditos: 3, desbloquea: [] },
  { id: "ing5", nombre: "Inglés V", creditos: 5, desbloquea: [] },
  { id: "datos", nombre: "Ciencia de Datos", creditos: 6, desbloquea: [] },
  { id: "global", nombre: "Globalización y Sustentabilidad", creditos: 6, desbloquea: [] },
  { id: "ops", nombre: "Gestión de Operaciones", creditos: 6, desbloquea: [] },
  { id: "marketing", nombre: "Marketing", creditos: 6, desbloquea: [] },
  { id: "cfg1", nombre: "CFG I", creditos: 5, desbloquea: [] },
];

// Guardar requisitos inversos
const requisitos = {};
cursos.forEach(c => {
  c.desbloquea.forEach(dest => {
    if (!requisitos[dest]) requisitos[dest] = [];
    requisitos[dest].push(c.id);
  });
});

// Estado inicial
const estado = {};
cursos.forEach(c => {
  estado[c.id] = requisitos[c.id] ? false : true; // desbloqueado si no tiene requisitos
});

// Crear los botones
const contenedor = document.getElementById("malla");

cursos.forEach(curso => {
  const div = document.createElement("div");
  div.className = `ramo ${estado[curso.id] ? "" : "bloqueado"}`;
  div.id = curso.id;

  const titulo = document.createElement("h3");
  titulo.textContent = curso.nombre;
  div.appendChild(titulo);

  const creditos = document.createElement("p");
  creditos.textContent = `${curso.creditos} créditos`;
  div.appendChild(creditos);

  const boton = document.createElement("button");
  boton.textContent = "Aprobar ramo";
  boton.disabled = !estado[curso.id];
  boton.onclick = () => aprobarRamo(curso.id);
  div.appendChild(boton);

  contenedor.appendChild(div);
});

// Función para aprobar un ramo
function aprobarRamo(id) {
  document.getElementById(id).classList.add("aprobado");
  document.getElementById(id).querySelector("button").disabled = true;

  // Desbloquear cursos que dependen de este
  cursos.forEach(curso => {
    if (requisitos[curso.id] && requisitos[curso.id].includes(id)) {
      const otrosReq = requisitos[curso.id].filter(req => {
        return document.getElementById(req).querySelector("button").disabled === false;
      });
      // Si todos los requisitos fueron aprobados
      if (otrosReq.length === 0) {
        estado[curso.id] = true;
        const bloque = document.getElementById(curso.id);
        bloque.classList.remove("bloqueado");
        bloque.querySelector("button").disabled = false;
      }
    }
  });
}
