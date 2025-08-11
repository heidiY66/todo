<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>待办清单（数据持久化）</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background: #f5f7fa;
            color: #333;
            line-height: 1.6;
            padding: 20px 10px 80px;
            max-width: 500px;
            margin: 0 auto;
        }
        h1 {
            text-align: center;
            margin-bottom: 20px;
            color: #2c3e50;
            font-size: 1.8rem;
        }
        .input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            position: sticky;
            top: 0;
            background: #f5f7fa;
            padding: 10px 0;
            z-index: 10;
        }
        #newTodo {
            flex: 1;
            padding: 14px 15px;
            border: 2px solid #3498db;
            border-radius: 12px;
            font-size: 1rem;
            outline: none;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        #addTodo {
            background: #3498db;
            color: white;
            border: none;
            border-radius: 12px;
            padding: 0 25px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            transition: background 0.3s;
        }
        #addTodo:active {
            background: #2980b9;
            transform: translateY(1px);
        }
        #todoList {
            list-style: none;
        }
        .todo-item {
            background: white;
            border-radius: 12px;
            padding: 16px;
            margin-bottom: 12px;
            display: flex;
            align-items: center;
            box-shadow: 0 3px 10px rgba(0,0,0,0.08);
            animation: fadeIn 0.3s ease;
        }
        .todo-text {
            flex: 1;
            padding: 0 15px;
            font-size: 1.1rem;
            word-break: break-word;
        }
        .todo-text.completed {
            text-decoration: line-through;
            color: #888;
        }
        .delete-button {
            background: #e74c3c;
            color: white;
            border: none;
            border-radius: 8px;
            padding: 10px 16px;
            font-size: 1rem;
            cursor: pointer;
            transition: background 0.3s;
        }
        .delete-button:active {
            background: #c0392b;
            transform: translateY(1px);
        }
        .empty-state {
            text-align: center;
            color: #7f8c8d;
            margin-top: 40px;
            font-style: italic;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .todo-checkbox {
            width: 20px;
            height: 20px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>我的待办清单</h1>
    <div class="input-group">
        <input type="text" id="newTodo" placeholder="输入新任务...">
        <button id="addTodo">添加</button>
    </div>
    <ul id="todoList">
        <!-- 任务将动态添加到这里 -->
    </ul>

    <script>
        // DOM元素引用
        const newTodoInput = document.getElementById('newTodo');
        const addTodoButton = document.getElementById('addTodo');
        const todoList = document.getElementById('todoList');
        
        // 页面加载时从localStorage加载任务
        window.addEventListener('DOMContentLoaded', loadTodos);

        // 添加任务功能
        addTodoButton.addEventListener('click', addTodo);
        newTodoInput.addEventListener('keypress', e => {
            if (e.key === 'Enter') addTodo();
        });

        // 添加新任务
        function addTodo() {
            const todoText = newTodoInput.value.trim();
            if (!todoText) return;
            
            createTodoItem(todoText, false);
            newTodoInput.value = '';
            newTodoInput.focus();
            saveTodos();
        }

        // 创建任务DOM元素
        function createTodoItem(text, completed) {
            const li = document.createElement('li');
            li.className = 'todo-item';
            
            // 创建复选框
            const checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.className = 'todo-checkbox';
            checkbox.checked = completed;
            
            // 创建任务文本
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = text;
            if (completed) textSpan.classList.add('completed');
            
            // 创建删除按钮
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-button';
            deleteBtn.textContent = '删除';
            
            // 添加元素到列表项
            li.appendChild(checkbox);
            li.appendChild(textSpan);
            li.appendChild(deleteBtn);
            todoList.appendChild(li);
            
            // 添加事件监听器
            checkbox.addEventListener('change', function() {
                textSpan.classList.toggle('completed', this.checked);
                saveTodos();
            });
            
            deleteBtn.addEventListener('click', function() {
                li.style.animation = 'fadeOut 0.3s ease';
                setTimeout(() => {
                    li.remove();
                    saveTodos();
                    checkEmptyState();
                }, 280);
            });
            
            checkEmptyState();
            return li;
        }

        // 保存所有任务到localStorage
        function saveTodos() {
            const todos = [];
            document.querySelectorAll('.todo-item').forEach(item => {
                const text = item.querySelector('.todo-text').textContent;
                const completed = item.querySelector('.todo-checkbox').checked;
                todos.push({ text, completed });
            });
            localStorage.setItem('todos', JSON.stringify(todos));
        }

        // 从localStorage加载任务
        function loadTodos() {
            const savedTodos = JSON.parse(localStorage.getItem('todos')) || [];
            todoList.innerHTML = '';
            
            if (savedTodos.length === 0) {
                checkEmptyState();
                return;
            }
            
            savedTodos.forEach(todo => {
                createTodoItem(todo.text, todo.completed);
            });
        }

        // 检查空状态
        function checkEmptyState() {
            const emptyState = document.querySelector('.empty-state');
            if (todoList.children.length === 0) {
                if (!emptyState) {
                    const msg = document.createElement('div');
                    msg.className = 'empty-state';
                    msg.textContent = '暂无任务，添加你的第一条待办吧！';
                    todoList.appendChild(msg);
                }
            } else if (emptyState) {
                emptyState.remove();
            }
        }
    </script>
</body>
</html>
