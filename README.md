# project-mongod
 # Auto detect text files and perform LF normalization
* text=auto
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task Management</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }

        input,
        button {
            margin: 5px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        th,
        td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }

        th {
            background-color: #f4f4f4;
        }

        .crad {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
            background-color: #f4f4f4;
        }
    </style>
</head>

<body>
    <h1>Task Management</h1>
    <input type="text" id="taskInput" placeholder="Enter task name">
    <button onclick="addTask()">Add Task</button>
    <table>
        <thead>
            <tr>
                <th>Task</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody id="taskList"></tbody>
    </table>

    <script>
        const apiUrl = 'http://localhost:3000/tasks';

        // Fetch tasks
        async function fetchTasks() {
            const res = await fetch(apiUrl);
            const tasks = await res.json();
            const taskList = document.getElementById('taskList');
            taskList.innerHTML = '';
            tasks.forEach(task => {
                const row = document.createElement('tr');
                row.innerHTML = `
          <td>${task.name}</td>
          <td>
            <button onclick="editTask('${task._id}', '${task.name}')">Edit</button>
            <button onclick="deleteTask('${task._id}')">Delete</button>
          </td>
        `;
                taskList.appendChild(row);
            });
        }

        // Add task
        async function addTask() {
            const taskInput = document.getElementById('taskInput');
            const name = taskInput.value;
            if (name) {
                await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ name }),
                });
                taskInput.value = '';
                fetchTasks();
            }
        }

        // Edit task
        async function editTask(id, oldName) {
            const newName = prompt('Edit task name:', oldName);
            if (newName) {
                await fetch(`${apiUrl}/${id}`, {
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ name: newName }),
                });
                fetchTasks();
            }
        }

        // Delete task
        async function deleteTask(id) {
            if (confirm('Are you sure you want to delete this task?')) {
                await fetch(`${apiUrl}/${id}`, { method: 'DELETE' });
                fetchTasks();
            }
        }

        // Initial load
        fetchTasks();
    </script>
</body>



</html>
