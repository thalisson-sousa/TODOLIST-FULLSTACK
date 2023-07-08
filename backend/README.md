<h1>API Projeto ToDo-List</h1>

<h2>Libs:</h2>
<p>express</p>
<p>msql2</p>
<p>dotenv</p>
<p>nodemon (como dev)</p>

<h2>Criar Bnaco de dados MYSQL</h2>

<h2>Step List</h2>
<ul>
    <li>Subir o servidor no app.js</li>
    <li>Criar as rotas no arquivo route.js</li>
    <li>Criar o arquivo de conexão com banco de dados connection.js</li>
    <li>Criar as variaveis de hambiente para usar no arquivo de conexão com o DBA</li>
    <li>Criar as controllers para Linkar as rotas com os serviços</li>
    <li>Criar em serviços as funçoes que seram passadas para o DBA</li>
</ul>

<h2>How Do</h2>

<h3>app.js:</h3>
<p>importar o express</p>
<p>importar o router</p>
<p>sobe o servidor com o express</p>
<p>Cria o arquivo de rotas e usa no app</p>

<code>
    const express = require('express');
const router = require('./router');

const app = express();

app.use(express.json());
app.use(router);

require('dotenv').config();

const PORT = process.env.PORT || 3333;

app.listen(PORT, () => console.log(`Server run port ${PORT}`));

module.exports = app;
</code>

<h3>route.js:</h3>
<p>importar o express</p>
<p>importar o router</p>
<p>Cria as rotas(get, post, update, delete) e chama as funçoes da controller</p>
<p>Cria os middleware para fazer as validações nos metodos (post e update)</p>

<code>
    const express = require('express');
const tasksController = require('./controllers/tasksController');
const tasksMiddleware = require('./middlewares/tasksMiddleware');

const router = express.Router();

router.get('/tasks', tasksController.getAll);
router.post('/tasks', tasksMiddleware.validateFieldTitle, tasksController.createTask);
router.delete('/tasks/:id', tasksController.deleteTask);
router.put('/tasks/:id', 
tasksMiddleware.validateFieldStatus, 
tasksMiddleware.validateFieldStatus, 
tasksController.updateTask);

module.exports = router;
</code>

<h3>controller.js:</h3>
<p>Importar os serviços</p>
<p>Criar as funções asyncronas que vao receber ou passar os dados vindos das rotas para os serviços</p>
<p></p>

<code>
    const tasksModel = require('../models/tasksModel');

const getAll = async (_req, res) => {
    const tasks = await tasksModel.getAll();
    return res.status(200).json(tasks);
};

const createTask = async(req, res) => {
    const createdTask = await tasksModel.createTask(req.body);
    return res.status(201).json(createdTask);
};

const deleteTask = async(req, res) => {
    const {id} = req.params;
    await tasksModel.deleteTask(id);
    return res.status(204).json();
}

const updateTask = async(req, res) => {
    const {id} = req.params;

    await tasksModel.updateTask(id, req.body);
    return res.status(204).json();
}

module.exports = {
    getAll,
    createTask,
    deleteTask,
    updateTask
};
</code>

<h3>services.js:</h3>
<p>Import o arquivo connection</p>
<p>Criar as funções asyncronas que iram usar o connection para manipular o DBA</p>
<ul>
    <li>Criar Metodo GET</li>
    <li>Criar Metodo POST</li>
    <li>Criar Metodo PUT</li>
    <li>Criar Metodo DELETE</li>
</ul>

<code>
const connection = require('./connection');

const getAll = async () => {
    const tasks = await connection.execute('SELECT * FROM tasks');
    return tasks[0];
}

const createTask = async (task) => {
    const {title} = task;

    const dataUTC = new Date(Date.now()).toUTCString();

    const query = 'INSERT INTO tasks(title, status, created_at) VALUES (?, ?, ?)';

    const createdTask = await connection.execute(query, [title, 'pendente', dataUTC]);
    return {insertId: createdTask[0].insertId};
};

const deleteTask = async (id) => {
    const removedTask = await connection.execute('DELETE FROM tasks WHERE id = ?', [id])
    return removedTask;
};

const updateTask = async (id, task) => {
    const {title, status} = task;

    const query = 'UPDATE tasks SET title = ?, status = ? WHERE id = ?';

    const updatedTask = await connection.execute(query, [title, status, id])
    return updatedTask;
};

module.exports = {
    getAll,
    createTask,
    deleteTask,
    updateTask
};
</code>

<p></p>
<p></p>
