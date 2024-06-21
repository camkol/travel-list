### Step-by-Step Instructions and Explanation:

1. **Setup the Project**:

   - Ensure you have Node.js installed.
   - Create a new React app using `create-react-app`:
     ```sh
     npx create-react-app packing-list
     cd packing-list
     ```

2. **Create the Necessary Components**:

   - You will need to create the following components: `App.js`, `Logo.js`, `Form.js`, `PackingList.js`, `Item.js`, and `Stats.js`.

3. **App.js**:

   - This is the main component that holds the state and renders all other components.
   - Create the `App.js` file:

     ```jsx
     import { useState } from "react";
     import Logo from "./Logo";
     import Form from "./Form";
     import PackingList from "./PackingList";
     import Stats from "./Stats";

     export default function App() {
       // State to manage the list of items
       const [items, setItems] = useState([]);

       // Function to handle adding items to the list
       function handleAddItems(item) {
         setItems((items) => [...items, item]);
       }

       // Function to handle deleting an item from the list
       function handleDeleteItem(id) {
         setItems((items) => items.filter((item) => item.id !== id));
       }

       // Function to handle toggling the packed status of an item
       function handleToggleItem(id) {
         setItems((items) =>
           items.map((item) =>
             item.id === id ? { ...item, packed: !item.packed } : item
           )
         );
       }

       // Function to clear the entire list
       function handleClearList() {
         const confirmed = window.confirm(
           "Are you sure you want to delete all items?"
         );
         if (confirmed) setItems([]);
       }

       return (
         <div className="app">
           <Logo />
           <Form onAddItems={handleAddItems} />
           <PackingList
             items={items}
             onDeleteItem={handleDeleteItem}
             onToggleItem={handleToggleItem}
             onClearList={handleClearList}
           />
           <Stats items={items} />
         </div>
       );
     }
     ```

4. **Logo.js**:

   - This component simply renders the logo.
   - Create the `Logo.js` file:
     ```jsx
     export default function Logo() {
       return <h1>🏝️ Far Away 🧳</h1>;
     }
     ```

5. **Form.js**:

   - This component allows the user to add new items to the packing list.
   - Create the `Form.js` file:

     ```jsx
     import { useState } from "react";

     export default function Form({ onAddItems }) {
       // State to manage the description and quantity of the item
       const [description, setDescription] = useState("");
       const [quantity, setQuantity] = useState(1);

       // Handle form submission
       function handleSubmit(e) {
         e.preventDefault();
         if (!description) return;
         const newItem = {
           description,
           quantity,
           packed: false,
           id: Date.now(),
         };
         onAddItems(newItem);
         setDescription("");
         setQuantity(1);
       }

       return (
         <form className="add-form" onSubmit={handleSubmit}>
           <h3>What do you need for your 😍 trip?</h3>
           <select
             value={quantity}
             onChange={(e) => setQuantity(Number(e.target.value))}
           >
             {Array.from({ length: 20 }, (_, i) => i + 1).map((num) => (
               <option value={num} key={num}>
                 {num}
               </option>
             ))}
           </select>
           <input
             type="text"
             placeholder="Item..."
             value={description}
             onChange={(e) => setDescription(e.target.value)}
           />
           <button>Add</button>
         </form>
       );
     }
     ```

6. **Item.js**:

   - This component represents a single item in the packing list.
   - Create the `Item.js` file:
     ```jsx
     export default function Item({ item, onDeleteItem, onToggleItem }) {
       return (
         <li>
           <input
             type="checkbox"
             checked={item.packed}
             onChange={() => onToggleItem(item.id)}
           />
           <span style={item.packed ? { textDecoration: "line-through" } : {}}>
             {item.quantity} {item.description}
           </span>
           <button onClick={() => onDeleteItem(item.id)}>❌</button>
         </li>
       );
     }
     ```

7. **PackingList.js**:

   - This component manages the list of items, including sorting and clearing the list.
   - Create the `PackingList.js` file:

     ```jsx
     import { useState } from "react";
     import Item from "./Item";

     export default function PackingList({
       items,
       onDeleteItem,
       onToggleItem,
       onClearList,
     }) {
       const [sortBy, setSortBy] = useState("input");

       // Sorting items based on the selected sorting option
       let sortedItems;
       if (sortBy === "input") sortedItems = items;
       if (sortBy === "description")
         sortedItems = items
           .slice()
           .sort((a, b) => a.description.localeCompare(b.description));
       if (sortBy === "packed")
         sortedItems = items
           .slice()
           .sort((a, b) => Number(a.packed) - Number(b.packed));

       return (
         <div className="list">
           <ul>
             {sortedItems.map((item) => (
               <Item
                 item={item}
                 onDeleteItem={onDeleteItem}
                 onToggleItem={onToggleItem}
                 key={item.id}
               />
             ))}
           </ul>
           <div className="actions">
             <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
               <option value="input">Sort by input order</option>
               <option value="description">Sort by description</option>
               <option value="packed">Sort by packed status</option>
             </select>
             <button onClick={onClearList}>Clear list</button>
           </div>
         </div>
       );
     }
     ```

8. **Stats.js**:

   - This component displays the statistics about the packing list.
   - Create the `Stats.js` file:

     ```jsx
     export default function Stats({ items }) {
       if (!items.length)
         return (
           <p className="stats">
             <em>Start adding some items to your packing list 🚀</em>
           </p>
         );

       const numItems = items.length;
       const numPacked = items.filter((item) => item.packed).length;
       const percentage = Math.round((numPacked / numItems) * 100);

       return (
         <footer className="stats">
           <em>
             {percentage === 100
               ? "You got everything! Ready to go ✈️"
               : ` 💼 You have ${numItems} items on your list, and you already packed ${numPacked} (${percentage}%)`}
           </em>
         </footer>
       );
     }
     ```

9. **Styling**:

   - You can add CSS to style the application. Create a `styles.css` file in the `src` folder and import it in `index.js`:

     ```css
     /* styles.css */
     .app {
       max-width: 600px;
       margin: 0 auto;
       padding: 20px;
       font-family: Arial, sans-serif;
     }

     .add-form,
     .list {
       margin-bottom: 20px;
     }

     .stats {
       text-align: center;
       margin-top: 20px;
     }

     .loader {
       text-align: center;
       margin-top: 20px;
     }

     .error {
       text-align: center;
       color: red;
       margin-top: 20px;
     }
     ```

   - Import the `styles.css` in `index.js`:

     ```jsx
     import React from "react";
     import ReactDOM from "react-dom/client";
     import "./styles.css";
     import App from "./App";

     const root = ReactDOM.createRoot(document.getElementById("root"));
     root.render(
       <React.StrictMode>
         <App />
       </React.StrictMode>
     );
     ```

10. **Run the App**:
    - Start the application using:
      ```sh
      npm start
      ```

This completes the setup of the packing list application. The application allows users to add items to a packing list, toggle their packed status, delete items, sort items, and clear the entire list. Each component is responsible for a specific part of the application's functionality, ensuring a clean and modular structure.
