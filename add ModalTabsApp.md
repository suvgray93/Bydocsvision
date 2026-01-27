### USER
Как на react написать приложения в виде модального окна состоящие из 3 окон (вкладок) переключения на которых происходит на нажатия кнопок в левом нижнем углу экрана. 

### ASSISTANT
Вот пример реализации React-приложения с модальным окном, содержащим три вкладки, переключение между которыми происходит по нажатию кнопок в левом нижнем углу экрана.

---

### ✅ Требования:
- Модальное окно (всегда открыто или управляется состоянием).
- Три вкладки (например: "Вкладка 1", "Вкладка 2", "Вкладка 3").
- Кнопки переключения — фиксированы в **левом нижнем углу**.
- При клике на кнопку — отображается соответствующая вкладка.

---

### 📁 Структура компонента

```tsx
// ModalTabsApp.tsx
import React, { useState } from 'react';
import './ModalTabsApp.css'; // стили

const ModalTabsApp = () => {
  const [activeTab, setActiveTab] = useState(0); // индекс активной вкладки

  const tabs = [
    { id: 0, title: 'Вкладка 1', content: 'Содержимое первой вкладки' },
    { id: 1, title: 'Вкладка 2', content: 'Содержимое второй вкладки' },
    { id: 2, title: 'Вкладка 3', content: 'Содержимое третьей вкладки' },
  ];

  return (
    <div className="modal-overlay">
      {/* Модальное окно */}
      <div className="modal-content">
        <h2>{tabs[activeTab].title}</h2>
        <p>{tabs[activeTab].content}</p>
      </div>

      {/* Кнопки в левом нижнем углу */}
      <div className="tab-buttons">
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => setActiveTab(tab.id)}
            className={activeTab === tab.id ? 'active' : ''}
          >
            {tab.title}
          </button>
        ))}
      </div>
    </div>
  );
};

export default ModalTabsApp;
```

---

### 🎨 Стили (`ModalTabsApp.css`)

```css
/* Полупрозрачный фон модального окна */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

/* Содержимое модального окна */
.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  max-width: 600px;
  width: 90%;
  text-align: center;
}

/* Контейнер кнопок в левом нижнем углу */
.tab-buttons {
  position: fixed;
  bottom: 20px;
  left: 20px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.tab-buttons button {
  padding: 8px 16px;
  border: 1px solid #ccc;
  background: #f0f0f0;
  cursor: pointer;
  border-radius: 4px;
}

.tab-buttons button.active {
  background: #007bff;
  color: white;
}
``---

### 🧪 Использование в приложении

В `App.tsx`:

```tsx
import React from 'react';
import ModalTabsApp from './ModalTabsApp';

function App() {
  return (
    <div className="App">
      {/* Другой контент вашего приложения */}
      <ModalTabsApp />
    </div>
  );
}

export default App;
```

---

### 💡 Возможные улучшения:
- Сделать модальное окно управляемым (через `isOpen` prop и `onClose`).
- Анимировать переходы между вкладками.
- Использовать `useCallback` для обработчиков, если будет много логики.
- Применить CSS-in-JS или Tailwind, если вы предпочитаете другой подход к стилям.

Если нужно — могу адаптировать под конкретную библиотеку (например, Material UI, Ant Design и т.п.) или сделать версию с маршрутизацией.

### USER
Напиши так чтобы в каждую вкладку можно было передать свой компонент с передачей параметров в него. 

### ASSISTANT
Конечно! Чтобы в каждую вкладку можно было передавать **свой компонент** с **уникальными пропсами**, лучше всего использовать **рендер-функцию или JSX-элемент** напрямую в конфигурации вкладок. Вот гибкая и чистая реализация:

---

### ✅ Пример: Модальное окно с кастомными компонентами во вкладках

```tsx
// ModalTabsApp.tsx
import React, { useState } from 'react';
import './ModalTabsApp.css';

// Тип для описания вкладки
interface TabConfig {
  id: number;
  title: string;
  component: React.ReactNode; // или () => React.ReactNode, если нужна ленивая загрузка
}

// Примеры кастомных компонентов (могут быть где угодно)
const ProfileTab = ({ name }: { name: string }) => (
  <div>
    <h3>Профиль пользователя</h3>
    <p>Имя: {name}</p>
  </div>
);

const SettingsTab = ({ theme, onToggle }: { theme: string; onToggle: () => void }) => (
  <div>
    <h3>Настройки</h3>
    <p>Текущая тема: {theme}</p>
    <button onClick={onToggle}>Сменить тему</button>
  </div>
);

const StatsTab = ({ score }: { score: number }) => (
  <div>
    <h3>Статистика</h3>
    <p>Ваш счёт: {score}</p>
  </div>
);

const ModalTabsApp = () => {
  const [activeTabId, setActiveTabId] = useState(0);
  const [theme, setTheme] = useState('светлая');

  // Конфигурация вкладок с передачей компонентов и пропсов
  const tabs: TabConfig[] = [
    {
      id: 0,
      title: 'Профиль',
      component: <ProfileTab name="Алексей" />,
    },
    {
      id: 1,
      title: 'Настройки',
      component: <SettingsTab theme={theme} onToggle={() => setTheme(theme === 'светлая' ? 'тёмная' : 'светлая')} />,
    },
    {
      id: 2,
      title: 'Статистика',
      component: <StatsTab score={42} />,
    },
  ];

  const activeTab = tabs.find(tab => tab.id === activeTabId)!;

  return (
    <div className="modal-overlay">
      {/* Модальное окно */}
      <div className="modal-content">
        <h2>{activeTab.title}</h2>
        <div className="tab-content">{activeTab.component}</div>
      </div>

      {/* Кнопки переключения в левом нижнем углу */}
      <div className="tab-buttons">
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => setActiveTabId(tab.id)}
            className={activeTabId === tab.id ? 'active' : ''}
          >
            {tab.title}
          </button>
        ))}
      </div>
    </div>
  );
};

export default ModalTabsApp;
```

---

### 📝 Пояснение:
- Каждая вкладка — это объект с `id`, `title` и `component`.
- `component` — уже **отрендеренный JSX-элемент** с нужными пропсами.
- Это позволяет передавать **любые компоненты с любыми данными**, включая обработчики (`onToggle`), состояния (`theme`), и т.д.
- Состояние (например, `theme`) управляется **в родителе** (`ModalTabsApp`), что соответствует принципам React.

---

### 💡 Альтернатива: ленивая функция (если нужно отложить рендер)

Если вы хотите избежать предварительного рендера всех вкладок (например, для оптимизации), можно хранить **компонент как функцию**:

```ts
interface TabConfig {
  id: number;
  title: string;
  render: () => React.ReactNode;
}
```

И использовать так:

```tsx
{
  id: 1,
  title: 'Настройки',
  render: () => <SettingsTab theme={theme} onToggle={...} />
}
```

А в основном рендере:

```tsx
<div className="tab-content">{activeTab.render()}</div>
```

Это полезно, если компоненты тяжёлые или делают запросы при монтировании.

---

Хочешь, чтобы я показал версию с ленивым рендером или интеграцией с контекстом/Redux?

### USER
На первой вкладке должна быть таблица  компаний контрагента с полями имя организации, inn, адрес, стрелка для перехода к филиалам организации. вверху  должен быть текстовая поля с вводим фильтра. При загрузке компонента происходит запрос для получением данных Талицы. При нажатии на стрелку идёт запрос на поиск дочерних организаций и выводиться она выводиться в таблицу. При вводе поиска идёт поисковый запрос с фильтром, его результат выводиться в таблицу. 

### ASSISTANT
Вот реализация первой вкладки модального окна с таблицей контрагентов, фильтрацией и возможностью просмотра филиалов (дочерних организаций) по клику на стрелку.

---

### 🧱 Архитектура компонента
- **Основной компонент**: `CounterpartiesTab`
- **Состояния**:
  - `counterparties` — текущий список отображаемых организаций.
  - `loading` — индикатор загрузки.
  - `searchQuery` — текст фильтра.
  - `parentId` — ID родительской организации (если мы в "режиме филиалов").
- **API-функции** (заглушки, но легко заменяются на `fetch`/`axios`):
  - `fetchCounterparties(parentId?, search?)` — получает список.
- **UI**:
  - Поле поиска вверху.
  - Таблица с колонками: имя, ИНН, адрес, стрелка (→).
  - При клике на стрелку — загружаются дочерние организации.

---

### ✅ Реализация

```tsx
// CounterpartiesTab.tsx
import React, { useState, useEffect } from 'react';

// Типы данных
interface Counterparty {
  id: string;
  name: string;
  inn: string;
  address: string;
  hasChildren: boolean; // можно использовать для показа/скрытия стрелки
}

// ЗАГЛУШКИ API — замените на вашу логику (например, axios)
const api = {
  // parentId = null → корневые контрагенты
  fetchCounterparties: async (parentId: string | null = null, search: string = ''): Promise<Counterparty[]> => {
    // Пример: имитация задержки
    await new Promise((r) => setTimeout(r, 500));

    // Здесь должен быть реальный запрос:
    // return axios.get('/api/counterparties', { params: { parentId, search } }).then(res => res.data);

    // Для демо — статические данные
    if (parentId === '1') {
      return [
        { id: '11', name: 'Филиал ООО "Ромашка" (Москва)', inn: '7712345678', address: 'г. Москва, ул. Ленина, 10', hasChildren: false },
        { id: '12', name: 'Филиал ООО "Ромашка" (СПб)', inn: '7812345679', address: 'г. Санкт-Петербург, Невский пр., 5', hasChildren: false },
      ];
    }

    return [
      { id: '1', name: 'ООО "Ромашка"', inn: '7701234567', address: 'г. Москва, ул. Тверская, 1', hasChildren: true },
      { id: '2', name: 'АО "Подсолнух"', inn: '7709876543', address: 'г. Москва, ул. Арбат, 15', hasChildren: false },
      { id: '3', name: 'ИП Иванов', inn: '771122334455', address: 'г. Казань, Баумана, 20', hasChildren: false },
    ].filter(item =>
      item.name.toLowerCase().includes(search.toLowerCase()) ||
      item.inn.includes(search)
    );
  },
};

const CounterpartiesTab = () => {
  const [counterparties, setCounterparties] = useState<Counterparty[]>([]);
  const [loading, setLoading] = useState(true);
  const [searchQuery, setSearchQuery] = useState('');
  const [parentId, setParentId] = useState<string | null>(null); // null = корень

  // Загрузка данных при монтировании и при изменении parentId/searchQuery
  useEffect(() => {
    const load = async () => {
      setLoading(true);
      try {
        const data = await api.fetchCounterparties(parentId, searchQuery);
        setCounterparties(data);
      } catch (err) {
        console.error('Ошибка загрузки контрагентов:', err);
        setCounterparties([]);
      } finally {
        setLoading(false);
      }
    };
    load();
  }, [parentId, searchQuery]);

  const handleViewChildren = (id: string) => {
    setParentId(id);
    setSearchQuery(''); // сбросить поиск при переходе
  };

  const handleGoBack = () => {
    setParentId(null);
    setSearchQuery('');
  };

  return (
    <div>
      {/* Кнопка "Назад", если мы в режиме филиалов */}
      {parentId && (
        <button onClick={handleGoBack} style={{ marginBottom: '12px' }}>
          ← Назад к родителям
        </button>
      )}

      {/* Поле поиска */}
      <input
        type="text"
        placeholder="Поиск по названию или ИНН..."
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
        style={{
          width: '100%',
          padding: '8px',
          marginBottom: '16px',
          borderRadius: '4px',
          border: '1px solid #ccc',
        }}
      />

      {/* Индикатор загрузки */}
      {loading ? (
        <p>Загрузка...</p>
      ) : (
        <table style={{ width: '100%', borderCollapse: 'collapse' }}>
          <thead>
            <tr>
              <th style={styles.th}>Наименование</th>
              <th style={styles.th}>ИНН</th>
              <th style={styles.th}>Адрес</th>
              <th style={styles.th}></th>
            </tr>
          </thead>
          <tbody>
            {counterparties.length === 0 ? (
              <tr>
                <td colSpan={4} style={{ textAlign: 'center', padding: '12px' }}>
                  Нет данных
                </td>
              </tr>
            ) : (
              counterparties.map((item) => (
                <tr key={item.id} style={styles.tr}>
                  <td style={styles.td}>{item.name}</td>
                  <td style={styles.td}>{item.inn}</td>
                  <td style={styles.td}>{item.address}</td>
                  <td style={styles.td}>
                    {item.hasChildren && (
                      <button
                        onClick={() => handleViewChildren(item.id)}
                        style={{
                          background: 'none',
                          border: 'none',
                          cursor: 'pointer',
                          fontSize: '18px',
                          color: '#007bff',
                        }}
                      >
                        →
                      </button>
                    )}
                  </td>
                </tr>
              ))
            )}
          </tbody>
        </table>
      )}
    </div>
  );
};

// Стили для таблицы
const styles = {
  th: {
    backgroundColor: '#f8f9fa',
    padding: '10px',
    textAlign: 'left' as const,
    borderBottom: '2px solid #dee2e6',
  },
  tr: {
    borderBottom: '1px solid #dee2e6',
  },
  td: {
    padding: '10px',
  },
};

export default CounterpartiesTab;
```

---

### 🔌 Как использовать в модальном окне из предыдущего примера

В `ModalTabsApp.tsx`:

```tsx
import CounterpartiesTab from './CounterpartiesTab';

// ...

const tabs: TabConfig[] = [
  {
    id: 0,
    title: 'Контрагенты',
    component: <CounterpartiesTab />,
  },
  // остальные вкладки...
];
```

---

### 💡 Что можно улучшить
- Заменить `api.fetchCounterparties` на реальный вызов через `fetch` или `axios`.
- Добавить debounce для поиска (чтобы не делать запрос при каждом символе).
- Использовать `React.memo` для строк таблицы, если данных много.
- Добавить пагинацию, если список большой.

Если нужно — могу добавить **debounce**, **интеграцию с Redux Toolkit Query / RTK Query**, или **реальный пример с `fetch`**.

### USER
Напиши компонент для другой вкладке. В ней есть поля для ввода нового контрагента в систему. Имя организации, Инн, КПП, адресс, email,  тип контрагента (может быть юрлицо, физлицо, ИП) , переключатель филиал или главная организация. Переключатель виден и обязателен для заполнения только для юридических лиц, для остальных типов скрыто. Для юрлиц обязательны поля Инн, КПП если это филиал  почтовый адресс, для ИП обязателен только инн. Для физлица только имя. Для физлиц и ip Инн 14 цифр(сделать маску) для юрлиц 9 цифр

### ASSISTANT
Вот компонент для второй вкладки — **форма добавления нового контрагента** с динамическими полями, валидацией и масками ИНН в зависимости от типа.

---

### ✅ Функционал:
- Выбор типа: **Юрлицо / ИП / Физлицо**
- Условное отображение:
  - Поле **КПП** — только для юрлиц
  - Переключатель **«Филиал / Главная организация»** — только для юрлиц
- Валидация:
  - Юрлицо: ИНН (10 цифр), КПП (9 цифр), адрес (если филиал)
  - ИП: ИНН (12 или 14 цифр — в РФ ИП 12, но вы указали 14 → будем считать 12/14 допустимыми)
  - Физлицо: только имя
- Маска ИНН:
  - Юрлицо: 10 цифр
  - ИП/Физлицо: до 14 цифр (только цифры)

> ⚠️ Примечание: в реальности ИНН:
> - Юрлицо — 10 цифр
> - ИП — 12 цифр
> - Физлицо без статуса ИП — 12 цифр  
> Но вы указали **14 для ИП/физлиц**, поэтому реализуем как **до 14 цифр**, но можно легко адаптировать.

---

### 📄 Компонент: `AddCounterpartyTab.tsx`

```tsx
// AddCounterpartyTab.tsx
import React, { useState } from 'react';

type CounterpartyType = 'legal' | 'individual' | 'self-employed';

const AddCounterpartyTab = () => {
  const [formData, setFormData] = useState({
    name: '',
    inn: '',
    kpp: '',
    address: '',
    email: '',
    type: 'legal' as CounterpartyType,
    isBranch: false,
  });

  const [errors, setErrors] = useState<Record<string, string>>({});

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLSelectElement>) => {
    const { name, value, type } = e.target;
    let newValue = value;

    // Маска ИНН: только цифры, ограничение по длине
    if (name === 'inn') {
      newValue = value.replace(/\D/g, '');
      if (formData.type === 'legal') {
        newValue = newValue.slice(0, 10);
      } else {
        newValue = newValue.slice(0, 14);
      }
    }

    // Для чекбокса
    if (type === 'checkbox') {
      const target = e.target as HTMLInputElement;
      setFormData((prev) => ({ ...prev, [name]: target.checked }));
      return;
    }

    setFormData((prev) => ({ ...prev, [name]: newValue }));
  };

  const validate = () => {
    const newErrors: Record<string, string> = {};

    if (!formData.name.trim()) {
      newErrors.name = 'Обязательное поле';
    }

    if (formData.type === 'legal') {
      if (!/^\d{10}$/.test(formData.inn)) {
        newErrors.inn = 'ИНН юрлица должен содержать 10 цифр';
      }
      if (!formData.kpp.trim()) {
        newErrors.kpp = 'КПП обязателен для юрлица';
      } else if (!/^\d{9}$/.test(formData.kpp)) {
        newErrors.kpp = 'КПП должен содержать 9 цифр';
      }
      if (formData.isBranch && !formData.address.trim()) {
        newErrors.address = 'Адрес обязателен для филиала';
      }
    } else if (formData.type === 'self-employed' || formData.type === 'individual') {
      if (formData.inn && !/^\d{12,14}$/.test(formData.inn)) {
        newErrors.inn = 'ИНН должен содержать 12–14 цифр';
      }
      // Для физлица ИНН не обязателен, но если есть — валиден
    }

    if (formData.email && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.email)) {
      newErrors.email = 'Некорректный email';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (validate()) {
      // Здесь отправка данных на сервер
      console.log('Отправка данных:', formData);
      alert('Контрагент успешно добавлен!');
    }
  };

  const isLegal = formData.type === 'legal';

  return (
    <div style={{ maxWidth: '600px', margin: '0 auto' }}>
      <h2>Добавить нового контрагента</h2>
      <form onSubmit={handleSubmit} style={{ display: 'flex', flexDirection: 'column', gap: '16px' }}>
        {/* Тип контрагента */}
        <div>
          <label>Тип контрагента:</label>
          <select
            name="type"
            value={formData.type}
            onChange={handleChange}
            style={{ width: '100%', padding: '8px', marginTop: '4px' }}
          >
            <option value="legal">Юридическое лицо</option>
            <option value="self-employed">Индивидуальный предприниматель (ИП)</option>
            <option value="individual">Физическое лицо</option>
          </select>
        </div>

        {/* Наименование */}
        <div>
          <label>Наименование *</label>
          <input
            type="text"
            name="name"
            value={formData.name}
            onChange={handleChange}
            style={{ width: '100%', padding: '8px', marginTop: '4px' }}
          />
          {errors.name && <span style={{ color: 'red' }}>{errors.name}</span>}
        </div>

        {/* ИНН */}
        <div>
          <label>ИНН</label>
          <input
            type="text"
            name="inn"
            value={formData.inn}
            onChange={handleChange}
            placeholder={
              isLegal
                ? '10 цифр (юрлицо)'
                : '12–14 цифр (ИП/физлицо)'
            }
            style={{ width: '100%', padding: '8px', marginTop: '4px' }}
          />
          {errors.inn && <span style={{ color: 'red' }}>{errors.inn}</span>}
        </div>

        {/* КПП — только для юрлиц */}
        {isLegal && (
          <div>
            <label>КПП *</label>
            <input
              type="text"
              name="kpp"
              value={formData.kpp}
              onChange={handleChange}
              placeholder="9 цифр"
              style={{ width: '100%', padding: '8px', marginTop: '4px' }}
            />
            {errors.kpp && <span style={{ color: 'red' }}>{errors.kpp}</span>}
          </div>
        )}

        {/* Адрес */}
        <div>
          <label>Адрес</label>
          <input
            type="text"
            name="address"
            value={formData.address}
            onChange={handleChange}
            style={{ width: '100%', padding: '8px', marginTop: '4px' }}
          />
          {errors.address && <span style={{ color: 'red' }}>{errors.address}</span>}
        </div>

        {/* Email */}
        <div>
          <label>Email</label>
          <input
            type="email"
            name="email"
            value={formData.email}
            onChange={handleChange}
            style={{ width: '100%', padding: '8px', marginTop: '4px' }}
          />
          {errors.email && <span style={{ color: 'red' }}>{errors.email}</span>}
        </div>

        {/* Переключатель "Филиал" — только для юрлиц */}
        {isLegal && (
          <div>
            <label>
              <input
                type="checkbox"
                name="isBranch"
                checked={formData.isBranch}
                onChange={handleChange}
                style={{ marginRight: '8px' }}
              />
              Это филиал (не главная организация)
            </label>
          </div>
        )}

        {/* Кнопка отправки */}
        <button
          type="submit"
          style={{
            padding: '10px',
            backgroundColor: '#007bff',
            color: 'white',
            border: 'none',
            borderRadius: '4px',
            cursor: 'pointer',
          }}
        >
          Добавить контрагента
        </button>
      </form>
    </div>
  );
};

export default AddCounterpartyTab;
```

---

### 🔧 Как использовать в модальном окне

В `ModalTabsApp.tsx`:

```tsx
import AddCounterpartyTab from './AddCounterpartyTab';

const tabs: TabConfig[] = [
  { id: 0, title: 'Контрагенты', component: <CounterpartiesTab /> },
  { id: 1, title: 'Добавить', component: <AddCounterpartyTab /> },
  // третья вкладка...
];
```

---

### 💡 Возможные улучшения:
- Использовать библиотеку форм (например, **React Hook Form**) для более сложной валидации.
- Добавить **маску ввода** через `imask` или `react-input-mask`.
- Сохранять черновик в `localStorage`.
- Отправлять данные через `fetch`/`axios`.

Если нужно — могу добавить **интеграцию с API**, **маску ввода с разделителями**, или **поддержку 12-значного ИНН для ИП**.


#######################

Отлично! Добавим **список выбранных контрагентов** внизу модального окна (вне вкладок), который:

- Общ для всех вкладок.
- Позволяет выбирать контрагентов из таблицы (первая вкладка).
- Позволяет добавлять новых контрагентов через форму (вторая вкладка).
- Имеет кнопку **«Очистить список»**.

Для этого состояние списка выбранных контрагентов должно управляться **в родительском компоненте `ModalTabsApp`**, а дочерние вкладки будут получать функции для добавления.

---

### 🧩 Шаг 1: Обновлённый `ModalTabsApp.tsx`

```tsx
// ModalTabsApp.tsx
import React, { useState } from 'react';
import './ModalTabsApp.css';

// Типы
interface Counterparty {
  id: string;
  name: string;
  inn: string;
  address: string;
}

interface TabConfig {
  id: number;
  title: string;
  component: React.ReactNode;
}

interface ModalTabsAppProps {
  onClose: () => void;
}

const ModalTabsApp = ({ onClose }: ModalTabsAppProps) => {
  const [activeTabId, setActiveTabId] = useState(0);
  const [selectedCounterparties, setSelectedCounterparties] = useState<Counterparty[]>([]);

  // Функция для добавления контрагента в общий список
  const addSelectedCounterparty = (counterparty: Counterparty) => {
    // Избегаем дубликатов по id
    if (!selectedCounterparties.some(c => c.id === counterparty.id)) {
      setSelectedCounterparties(prev => [...prev, counterparty]);
    }
  };

  const clearSelection = () => {
    setSelectedCounterparties([]);
  };

  const tabs: TabConfig[] = [
    {
      id: 0,
      title: 'Контрагенты',
      component: <CounterpartiesTab onAdd={addSelectedCounterparty} />,
    },
    {
      id: 1,
      title: 'Добавить',
      component: <AddCounterpartyTab onAdd={addSelectedCounterparty} />,
    },
    {
      id: 2,
      title: 'Отчёты',
      component: <div>Скоро здесь будут отчёты</div>,
    },
  ];

  const activeTab = tabs.find(tab => tab.id === activeTabId)!;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        {/* Крестик */}
        <button className="modal-close-button" onClick={onClose}>
          ×
        </button>

        <h2 style={{ marginBottom: '16px' }}>{activeTab.title}</h2>
        <div className="tab-content">{activeTab.component}</div>
      </div>

      {/* Кнопки вкладок */}
      <div className="tab-buttons">
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => setActiveTabId(tab.id)}
            className={activeTabId === tab.id ? 'active' : ''}
          >
            {tab.title}
          </button>
        ))}
      </div>

      {/* Список выбранных контрагентов — ВНИЗУ МОДАЛЬНОГО ОКНА */}
      <div className="selected-counterparties-panel">
        <div className="selected-header">
          <h3>Выбранные контрагенты ({selectedCounterparties.length})</h3>
          <button onClick={clearSelection} className="clear-button">
            Очистить
          </button>
        </div>
        {selectedCounterparties.length === 0 ? (
          <p>Нет выбранных контрагентов</p>
        ) : (
          <ul className="selected-list">
            {selectedCounterparties.map((c) => (
              <li key={c.id} className="selected-item">
                <strong>{c.name}</strong> | ИНН: {c.inn} | {c.address}
              </li>
            ))}
          </ul>
        )}
      </div>
    </div>
  );
};

// Ленивые импорты (или обычные, если не используете code splitting)
const CounterpartiesTab = React.lazy(() => import('./CounterpartiesTab'));
const AddCounterpartyTab = React.lazy(() => import('./AddCounterpartyTab'));

export default ModalTabsApp;
```

---

### 🎨 Шаг 2: Добавим стили для панели выбранных (`ModalTabsApp.css`)

```css
/* ... предыдущие стили ... */

/* Панель выбранных контрагентов внизу */
.selected-counterparties-panel {
  position: fixed;
  bottom: 100px; /* выше кнопок вкладок */
  left: 20px;
  right: 20px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 12px;
  box-shadow: 0 -2px 8px rgba(0,0,0,0.1);
  max-height: 200px;
  overflow-y: auto;
  z-index: 1001;
}

.selected-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.clear-button {
  padding: 4px 10px;
  background: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.clear-button:hover {
  background: #c82333;
}

.selected-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.selected-item {
  padding: 6px 0;
  border-bottom: 1px solid #eee;
  font-size: 14px;
}

.selected-item:last-child {
  border-bottom: none;
}
```

---

### 🔧 Шаг 3: Обновление `CounterpartiesTab.tsx` — добавление выбора

В таблице добавим **кнопку "Выбрать"** в каждую строку:

```tsx
// Внутри CounterpartiesTab.tsx, в рендере строки таблицы
// Замените стрелку → на кнопку "Выбрать", или добавьте её

// Добавьте пропс:
interface CounterpartiesTabProps {
  onAdd: (counterparty: Counterparty) => void;
}

// Внутри map:
{counterparties.map((item) => (
  <tr key={item.id} style={styles.tr}>
    <td style={styles.td}>{item.name}</td>
    <td style={styles.td}>{item.inn}</td>
    <td style={styles.td}>{item.address}</td>
    <td style={styles.td}>
      {/* Стрелка для филиалов */}
      {item.hasChildren && (
        <button onClick={() => handleViewChildren(item.id)} style={{ marginRight: '8px' }}>
          →
        </button>
      )}
      {/* Кнопка выбора */}
      <button
        onClick={() => onAdd(item)}
        style={{
          background: '#28a745',
          color: 'white',
          border: 'none',
          borderRadius: '4px',
          padding: '4px 8px',
          cursor: 'pointer',
          fontSize: '12px',
        }}
      >
        Выбрать
      </button>
    </td>
  </tr>
))}
```

И не забудьте принять `onAdd` в компоненте:

```tsx
const CounterpartiesTab = ({ onAdd }: CounterpartiesTabProps) => {
  // ... остальной код
};
```

---

### 🔧 Шаг 4: Обновление `AddCounterpartyTab.tsx` — добавление после создания

В форме, после успешного добавления, вызываем `onAdd`:

```tsx
// Внутри AddCounterpartyTab.tsx

interface AddCounterpartyTabProps {
  onAdd: (counterparty: Counterparty) => void;
}

// В handleSubmit:
if (validate()) {
  const newCounterparty: Counterparty = {
    id: 'temp-' + Date.now(), // в реальности — ID с сервера
    name: formData.name,
    inn: formData.inn,
    address: formData.address,
    // можно добавить email и т.д., если нужно
  };
  onAdd(newCounterparty);
  alert('Контрагент добавлен в список!');
  // Опционально: сбросить форму
  setFormData(initialState);
}
```

---

### ✅ Результат:
- При клике **«Выбрать»** в таблице — контрагент попадает в общий список.
- При создании нового — он тоже добавляется в список.
- Список отображается **внизу модального окна**, над кнопками вкладок.
- Есть кнопка **«Очистить»**.

---

Если нужно:
- Удалять отдельные элементы из списка — могу добавить крестик у каждого.
- Передавать весь список наверх при закрытии — просто вызывайте `onClose(selectedCounterparties)`.

Хочешь реализовать **множественный выбор через чекбоксы** или **экспорт списка**?

Отлично! Реализуем **множественный выбор контрагентов через чекбоксы** в таблице первой вкладки. Пользователь сможет:

- Отмечать несколько строк.
- Нажимать кнопку **«Добавить выбранные»**, чтобы добавить их в общий список.
- Чекбоксы будут синхронизированы с состоянием (включая «Выбрать всё»).

---

### 🧩 Шаг 1: Обновлённый `CounterpartiesTab.tsx`

```tsx
// CounterpartiesTab.tsx
import React, { useState, useEffect, useCallback } from 'react';

interface Counterparty {
  id: string;
  name: string;
  inn: string;
  address: string;
  hasChildren: boolean;
}

interface CounterpartiesTabProps {
  onAdd: (counterparties: Counterparty[]) => void;
}

const api = {
  fetchCounterparties: async (parentId: string | null = null, search: string = '') => {
    await new Promise((r) => setTimeout(r, 300));
    if (parentId === '1') {
      return [
        { id: '11', name: 'Филиал ООО "Ромашка" (Москва)', inn: '7712345678', address: 'г. Москва, ул. Ленина, 10', hasChildren: false },
        { id: '12', name: 'Филиал ООО "Ромашка" (СПб)', inn: '7812345679', address: 'г. Санкт-Петербург, Невский пр., 5', hasChildren: false },
      ];
    }
    return [
      { id: '1', name: 'ООО "Ромашка"', inn: '7701234567', address: 'г. Москва, ул. Тверская, 1', hasChildren: true },
      { id: '2', name: 'АО "Подсолнух"', inn: '7709876543', address: 'г. Москва, ул. Арбат, 15', hasChildren: false },
      { id: '3', name: 'ИП Иванов', inn: '771122334455', address: 'г. Казань, Баумана, 20', hasChildren: false },
    ].filter(item =>
      item.name.toLowerCase().includes(search.toLowerCase()) ||
      item.inn.includes(search)
    );
  },
};

const CounterpartiesTab = ({ onAdd }: CounterpartiesTabProps) => {
  const [counterparties, setCounterparties] = useState<Counterparty[]>([]);
  const [loading, setLoading] = useState(true);
  const [searchQuery, setSearchQuery] = useState('');
  const [parentId, setParentId] = useState<string | null>(null);
  const [selectedIds, setSelectedIds] = useState<Set<string>>(new Set());

  // Загрузка данных
  useEffect(() => {
    const load = async () => {
      setLoading(true);
      try {
        const data = await api.fetchCounterparties(parentId, searchQuery);
        setCounterparties(data);
        // Сбросить выделение при смене данных
        setSelectedIds(new Set());
      } catch (err) {
        console.error(err);
        setCounterparties([]);
      } finally {
        setLoading(false);
      }
    };
    load();
  }, [parentId, searchQuery]);

  // Обработчики чекбоксов
  const toggleSelect = useCallback((id: string) => {
    setSelectedIds(prev => {
      const newSet = new Set(prev);
      if (newSet.has(id)) {
        newSet.delete(id);
      } else {
        newSet.add(id);
      }
      return newSet;
    });
  }, []);

  const toggleSelectAll = useCallback(() => {
    if (selectedIds.size === counterparties.length) {
      setSelectedIds(new Set());
    } else {
      setSelectedIds(new Set(counterparties.map(c => c.id)));
    }
  }, [counterparties, selectedIds.size]);

  const handleAddSelected = () => {
    const selected = counterparties.filter(c => selectedIds.has(c.id));
    if (selected.length > 0) {
      onAdd(selected);
      setSelectedIds(new Set()); // сбросить после добавления
    }
  };

  const handleViewChildren = (id: string) => {
    setParentId(id);
    setSearchQuery('');
    setSelectedIds(new Set()); // сброс при переходе
  };

  const handleGoBack = () => {
    setParentId(null);
    setSearchQuery('');
    setSelectedIds(new Set());
  };

  const isAllSelected = counterparties.length > 0 && selectedIds.size === counterparties.length;

  return (
    <div>
      {parentId && (
        <button onClick={handleGoBack} style={{ marginBottom: '12px' }}>
          ← Назад к родителям
        </button>
      )}

      <input
        type="text"
        placeholder="Поиск по названию или ИНН..."
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
        style={{
          width: '100%',
          padding: '8px',
          marginBottom: '16px',
          borderRadius: '4px',
          border: '1px solid #ccc',
        }}
      />

      {/* Кнопка "Добавить выбранные" */}
      {counterparties.length > 0 && (
        <button
          onClick={handleAddSelected}
          disabled={selectedIds.size === 0}
          style={{
            marginBottom: '12px',
            padding: '6px 12px',
            backgroundColor: selectedIds.size > 0 ? '#28a745' : '#ddd',
            color: 'white',
            border: 'none',
            borderRadius: '4px',
            cursor: selectedIds.size > 0 ? 'pointer' : 'not-allowed',
          }}
        >
          Добавить выбранные ({selectedIds.size})
        </button>
      )}

      {loading ? (
        <p>Загрузка...</p>
      ) : (
        <table style={{ width: '100%', borderCollapse: 'collapse' }}>
          <thead>
            <tr>
              {/* Чекбокс "Выбрать всё" */}
              <th style={styles.th}>
                <input
                  type="checkbox"
                  checked={isAllSelected}
                  onChange={toggleSelectAll}
                />
              </th>
              <th style={styles.th}>Наименование</th>
              <th style={styles.th}>ИНН</th>
              <th style={styles.th}>Адрес</th>
              <th style={styles.th}></th>
            </tr>
          </thead>
          <tbody>
            {counterparties.length === 0 ? (
              <tr>
                <td colSpan={5} style={{ textAlign: 'center', padding: '12px' }}>
                  Нет данных
                </td>
              </tr>
            ) : (
              counterparties.map((item) => (
                <tr key={item.id} style={styles.tr}>
                  <td style={styles.td}>
                    <input
                      type="checkbox"
                      checked={selectedIds.has(item.id)}
                      onChange={() => toggleSelect(item.id)}
                    />
                  </td>
                  <td style={styles.td}>{item.name}</td>
                  <td style={styles.td}>{item.inn}</td>
                  <td style={styles.td}>{item.address}</td>
                  <td style={styles.td}>
                    {item.hasChildren && (
                      <button
                        onClick={() => handleViewChildren(item.id)}
                        style={{
                          background: 'none',
                          border: 'none',
                          cursor: 'pointer',
                          fontSize: '18px',
                          color: '#007bff',
                        }}
                      >
                        →
                      </button>
                    )}
                  </td>
                </tr>
              ))
            )}
          </tbody>
        </table>
      )}
    </div>
  );
};

const styles = {
  th: {
    backgroundColor: '#f8f9fa',
    padding: '10px',
    textAlign: 'left' as const,
    borderBottom: '2px solid #dee2e6',
  },
  tr: {
    borderBottom: '1px solid #dee2e6',
  },
  td: {
    padding: '10px',
  },
};

export default CounterpartiesTab;
```

---

### 🔧 Шаг 2: Обновление `ModalTabsApp.tsx` — поддержка массива

Убедитесь, что `onAdd` принимает **массив**:

```tsx
// В ModalTabsApp.tsx
const addSelectedCounterparty = (counterparties: Counterparty[]) => {
  const unique = counterparties.filter(
    c => !selectedCounterparties.some(sc => sc.id === c.id)
  );
  if (unique.length > 0) {
    setSelectedCounterparties(prev => [...prev, ...unique]);
  }
};
```

(Это уже реализовано в предыдущей версии, но убедитесь, что тип `onAdd` — `(counterparties: Counterparty[]) => void`)

---

### ✅ Что изменилось:
- В таблице появился **чекбокс в каждой строке** и **«Выбрать всё»** в шапке.
- Кнопка **«Добавить выбранные (N)»** над таблицей.
- После добавления — выделение сбрасывается.
- При переходе к филиалам или смене поиска — выделение тоже сбрасывается (чтобы не было путаницы).

---

### 💡 Возможные улучшения:
- Сохранять выделение при возврате к родителю (через стек).
- Добавить индикацию уже выбранных контрагентов (например, серый фон строки).
- Поддержка клавиатуры (Shift + клик для диапазона).

Хочешь добавить **индикацию уже добавленных контрагентов** прямо в таблице?
