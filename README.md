# Bydocsvision
Перенос скриптов 

Вот пример простого модального окна с таблицей на React с использованием функциональных компонентов и хуков:

```jsx
import React, { useState } from 'react';
import './ModalWindow.css'; // Стили вынесены в отдельный файл

const ModalWindow = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [tableData, setTableData] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  // Функция для открытия/закрытия модального окна
  const toggleModal = () => {
    setIsModalOpen(!isModalOpen);
    if (isModalOpen) {
      // При закрытии модалки очищаем данные
      setTableData([]);
      setError(null);
    }
  };

  // Функция для загрузки данных
  const fetchTableData = async () => {
    setIsLoading(true);
    setError(null);
    
    try {
      // Имитация запроса к API
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      if (!response.ok) throw new Error('Ошибка загрузки данных');
      const data = await response.json();
      
      // Преобразуем данные для таблицы (возьмем только нужные поля)
      const formattedData = data.map(user => ({
        id: user.id,
        name: user.name,
        email: user.email,
        city: user.address.city,
        company: user.company.name
      }));
      
      setTableData(formattedData);
    } catch (err) {
      setError(err.message);
      console.error('Ошибка:', err);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <div className="app">
      <h1>Пример модального окна</h1>
      <button 
        className="open-modal-btn"
        onClick={toggleModal}
      >
        Открыть модальное окно
      </button>

      {isModalOpen && (
        <div className="modal-overlay">
          <div className="modal-content">
            <div className="modal-header">
              <h2>Данные таблицы</h2>
              <button 
                className="close-btn"
                onClick={toggleModal}
              >
                &times;
              </button>
            </div>

            <div className="modal-body">
              <div className="controls">
                <button 
                  className="fetch-btn"
                  onClick={fetchTableData}
                  disabled={isLoading}
                >
                  {isLoading ? 'Загрузка...' : 'Загрузить данные'}
                </button>
                
                {tableData.length > 0 && (
                  <div className="data-info">
                    Загружено записей: {tableData.length}
                  </div>
                )}
              </div>

              {error && (
                <div className="error-message">
                  Ошибка: {error}
                </div>
              )}

              {tableData.length > 0 ? (
                <div className="table-container">
                  <table className="data-table">
                    <thead>
                      <tr>
                        <th>ID</th>
                        <th>Имя</th>
                        <th>Email</th>
                        <th>Город</th>
                        <th>Компания</th>
                      </tr>
                    </thead>
                    <tbody>
                      {tableData.map(item => (
                        <tr key={item.id}>
                          <td>{item.id}</td>
                          <td>{item.name}</td>
                          <td>{item.email}</td>
                          <td>{item.city}</td>
                          <td>{item.company}</td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              ) : !error && !isLoading && (
                <div className="no-data">
                  Данные не загружены. Нажмите "Загрузить данные"
                </div>
              )}
            </div>

            <div className="modal-footer">
              <button 
                className="close-modal-btn"
                onClick={toggleModal}
              >
                Закрыть
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

export default ModalWindow;
```

И соответствующий файл стилей `ModalWindow.css`:

```css
.app {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.open-modal-btn {
  padding: 12px 24px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}

.open-modal-btn:hover {
  background-color: #45a049;
}

/* Стили для оверлея модального окна */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

/* Стили для контента модального окна */
.modal-content {
  background-color: white;
  border-radius: 8px;
  width: 90%;
  max-width: 800px;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
}

.modal-header {
  padding: 20px;
  border-bottom: 1px solid #e0e0e0;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-header h2 {
  margin: 0;
  color: #333;
}

.close-btn {
  background: none;
  border: none;
  font-size: 28px;
  cursor: pointer;
  color: #666;
  line-height: 1;
  padding: 0;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.close-btn:hover {
  color: #333;
}

.modal-body {
  padding: 20px;
  overflow-y: auto;
  flex-grow: 1;
}

.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
  gap: 10px;
}

.fetch-btn {
  padding: 10px 20px;
  background-color: #2196F3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.fetch-btn:hover:not(:disabled) {
  background-color: #0b7dda;
}

.fetch-btn:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.data-info {
  color: #666;
  font-size: 14px;
}

.error-message {
  background-color: #ffebee;
  color: #c62828;
  padding: 12px;
  border-radius: 4px;
  margin-bottom: 20px;
  border-left: 4px solid #c62828;
}

.no-data {
  text-align: center;
  padding: 40px;
  color: #999;
  font-size: 16px;
  background-color: #f9f9f9;
  border-radius: 4px;
}

/* Стили для таблицы */
.table-container {
  overflow-x: auto;
}

.data-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
}

.data-table th {
  background-color: #f5f5f5;
  padding: 12px;
  text-align: left;
  font-weight: bold;
  color: #333;
  border-bottom: 2px solid #ddd;
}

.data-table td {
  padding: 12px;
  border-bottom: 1px solid #eee;
}

.data-table tr:hover {
  background-color: #f9f9f9;
}

.data-table tr:last-child td {
  border-bottom: none;
}

.modal-footer {
  padding: 20px;
  border-top: 1px solid #e0e0e0;
  display: flex;
  justify-content: flex-end;
}

.close-modal-btn {
  padding: 10px 24px;
  background-color: #757575;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.close-modal-btn:hover {
  background-color: #616161;
}

/* Адаптивность */
@media (max-width: 768px) {
  .modal-content {
    width: 95%;
    margin: 10px;
  }
  
  .data-table {
    font-size: 14px;
  }
  
  .data-table th,
  .data-table td {
    padding: 8px;
  }
}
```

## Особенности реализации:

1. **Состояния компонента**:
   - `isModalOpen` - управление видимостью модального окна
   - `tableData` - данные для таблицы
   - `isLoading` - состояние загрузки
   - `error` - обработка ошибок

2. **Логика работы**:
   - При нажатии на кнопку "Открыть модальное окно" открывается модалка
   - Внутри модалки есть кнопка "Загрузить данные" для получения данных с API
   - Используется JSONPlaceholder API для демонстрационных данных
   - Данные отображаются в таблице после загрузки

3. **Дополнительные функции**:
   - Индикатор загрузки
   - Обработка ошибок
   - Адаптивный дизайн
   - Очистка данных при закрытии модалки

4. **Стилизация**:
   - Простой и чистый дизайн
   - Эффекты hover для интерактивных элементов
   - Адаптивность для мобильных устройств

Для использования этого компонента просто импортируйте его в ваш основной компонент:

```jsx
import React from 'react';
import ModalWindow from './ModalWindow';

function App() {
  return (
    <div className="App">
      <ModalWindow />
    </div>
  );
}

export default App;
```

Этот пример можно расширить, добавив:
- Пагинацию для таблицы
- Фильтрацию и сортировку данных
- Более сложные анимации
- Параметризацию запроса данных
