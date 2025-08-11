<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>手机待办清单</title>
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
            padding: 20px 10px 80px; /* 底部留出按钮空间 */
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
            padding-right: 15px;
            font-size: 1.1rem;
            word-break: break-word;
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
        
        // 添加任务功能
        addTodoButton.addEventListener('click', addTodo);
        newTodoInput.addEventListener('keypress', e => {
            if (e.key === 'Enter') addTodo();
        });
        
        function addTodo() {
            const todoText = newTodoInput.value.trim();
            if (!todoText) return;
            
            const li = document.createElement('li');
            li.className = 'todo-item';
            li.innerHTML = `
                <span class="todo-text">${todoText}</span>
                <button class="delete-button">删除</button>
            `;
            
            // 添加删除功能
            li.querySelector('.delete-button').addEventListener('click', () => {
                li.style.animation = 'fadeOut 0.3s ease';
                setTimeout(() => li.remove(), 280);
                checkEmptyState();
            });
            
            todoList.appendChild(li);
            newTodoInput.value = '';
            newTodoInput.focus();
            checkEmptyState();
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
        
        // 初始空状态检查
        checkEmptyState();
    </script>
</body>
</html># todo
