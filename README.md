# Goals

- Provide a **simple, generalized tool** for collecting data into Google Sheets with minimal friction.  
- Reduce input steps: **open → type → choose category → auto-fill date → submit**.  
- Support **flexible use cases** like journaling, expense tracking, habit logging, or quick notes.  
- Leverage **AppSheet** for a no-code, mobile-friendly interface.  
- Keep the setup **extensible** so users can add fields, categories, and validation as needed.  
- Document the process so others can **replicate or adapt** it easily.  
- Evolve into a full tutorial + shareable template over time.


# Data Collector — Part 1  
*Building the foundation for personal finance tracking*

This project shows how to assemble a simple data collection app in AppSheet that writes directly into Google Sheets.  
The goal is to make expense and income logging effortless, so data quality stays high and consistent.

---

## 🏁 All you need to get started

- A **Google account** (free)  
- Access to **Google Sheets**  
- An **AppSheet account** (linked to your Google account, free tier works)  
- 30 minutes of focused time  

That’s it — no code required.

![screenshot of appsheet sign-in](prints/appsheet-signin.png)

---

## 📦 Box Contents  

- 1 Google Sheet with 3 tabs:  
  - `Expenses` → for spending  
  - `Income` → for earnings  
  - `Staging` → temporary input buffer  

- 2 lookup tabs for categories:  
  - `ExpensesTypes` → distinct expense types  
  - `IncomeTypes` → distinct income types  

*(Think of these as your wooden panels and screws)*  

![screenshot of google sheet with all 5 tabs](prints/sheet-tabs.png)

---

## 🪛 Step 1: Connect to AppSheet  

- Go to [AppSheet](https://www.appsheet.com/)  
- **Start with your own data** → select your spreadsheet  
- In **Data → Tables**, add all 5 sheets  
- Regenerate each table so AppSheet recognizes the columns  

![screenshot of appsheet tables](prints/appsheet-tables.png)

---

## 🔩 Step 2: Configure Columns  

### In `Staging`  

- **Mode**  
  - Type: Enum  
  - Values: `Expenses`, `Income`  
  - Input mode: Buttons  

- **Label** → Text  

- **Type**  
  - Type: Enum  
  - Input mode: Dropdown  
  - **Valid_If**:  
    ```appsheet
    SWITCH(
      [Mode],
      "Expenses", SORT(ExpensesTypes[Expenses Type]),
      "Income",   SORT(IncomeTypes[Income Type]),
      LIST()
    )
    ```

- **Value** → Decimal (or Price)  

- **Date** → Date  
  - Initial value: `TODAY()`  

![screenshot of column config](prints/staging-columns.png)

---

## 🔨 Step 3: Build the Form  

- Go to **UX → Views → + New view**  
  - Name: `Data Entry`  
  - For this data: `Staging`  
  - View type: Form  

- Column order: `Mode → Label → Type → Value → Date`  

![screenshot of form view](prints/staging-form.png)

---

## 🔗 Step 4: Add the Actions  

Create 4 actions in **Behavior → Actions**:  

1. **Add to Expenses**  
   - For: `Staging`  
   - Do this: *Data: add new row to another table* (`Expenses`)  
   - Map: Date=[Date], Label=[Label], Type=[Type], Value=[Value]  
   - Only if: `[Mode]="Expenses"`  

2. **Add to Income**  
   - Same, target = `Income`  
   - Only if: `[Mode]="Income"`  

3. **Delete Staging row**  
   - For: `Staging`  
   - Do this: *Data: delete this row*  

4. **Submit (Grouped)**  
   - For: `Staging`  
   - Do this: *Grouped: execute a sequence of actions*  
   - Actions: Add to Expenses, Add to Income, Delete Staging  

![screenshot of actions list](prints/appsheet-actions.png)

---

## 🖲 Step 5: Hook Submit to the Form  

- Go to **UX → Views → Data Entry (Form)**  
- Under **Event actions → Form Saved** select `Submit`  
- Set **Auto save = ON** for smoother experience  

![screenshot of form saved config](prints/form-saved.png)

---

## ✅ Step 6: Test  

- Open the app preview (phone view)  
- Select **Expenses** → fill out form → Save  
  - Row should appear in `Expenses`, staging clears  
- Select **Income** → repeat  

![screenshot of sheet updating with new row](prints/sheet-updated.png)

---

## 🎉 Result  

You now have:  
- A fast, structured data entry app  
- A clean database in Google Sheets  
- Categories pulled dynamically from lookup tables  

**Next (Part 2):** connect this database to reporting tools (Looker Studio) to generate insights.  

---
