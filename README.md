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

        button {
            padding: 5px 10px;
            margin: 0 5px;
        }

        .edit {
            background-color: #ffc107;
            border: none;
            color: white;
        }

        .delete {
            background-color: #dc3545;
            border: none;
            color: white;
        }
    </style>
</head>

<body>
    <h1>Task Management</h1>
    <input type="text" id="taskInput" placeholder="Enter task name">
    <button onclick="addTask()">Add Task</button>
    <button onclick="deleteTask()">Delete Task</button><!-- ปุ้มลบ-->
    <button onclick="editTask()">Edit Task</button> <!-- ปุ้มแก้ไข -->
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
                row.innerHTML =
                    <tr>
                        <td>${task.name}</td>
                        <td>
                            <button class="edit" onclick="editTask('${task._id}', '${task.name}')">Edit</button>
                            <button class="delete" onclick="deleteTask('${task._id}')">Delete</button>
                        </td>
                    </tr>
                    ;
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
    </script>
</body>

</html>