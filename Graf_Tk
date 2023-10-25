from ast import Pass
import tkinter as tk
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg, NavigationToolbar2Tk
from tkinter import filedialog
from math import *
import MatrixClass as Matrix

class ParameterManager:
    matrix_instance = Matrix.Matrix()  # Создаем статический экземпляр класса Matrix с размером 3x3.
    file_name = "File.txt"  # Задаем имя файла по умолчанию для сохранения данных.
    Job_file="out.txt"
    
    def __init__(self, root, parameters=None, operations=None,Graph=None,Give_Text=None,Func_name=None ):
        self.root = root 
        self.Graph = Graph or False
        self.Give_Text=Give_Text or False
        self.parameters = parameters or {}  
        self.buttons = {}  
        self.entries = {}
        self.Func_name=Func_name or False
        
        self.operations_list= {
        "set_parameters": self.set_parameters,
        "calculate_parametrs": self.calculate_parametrs,
        "draw_graph": self.draw_graph,
        "write_to_file": self.write_to_file,
        "read_from_file": self.read_from_file,
        "save_graph": self.save_graph,
        "clear_graph": self.clear_graph
         }

        self.assign_values(self.operations_list, operations)
        self.operations = operations or {} 
        

        self.create_widgets() 
        

    def assign_values(self, source_dict, target_dict):
        for key, value in source_dict.items():
            if key in target_dict:
               target_dict[key]["function"] = value
        
    def enable_buttons(self, *button_names):
        
        button_names = list(button_names)  # Преобразовать кортеж в список
        for name in button_names:
            button = self.buttons.get(name)  # Получаем кнопку по имени.
            if button:
                button.config(state="active")  # Включаем кнопку.

    def disable_buttons(self, *button_names):
        
        button_names = list(button_names)  # Преобразовать кортеж в список
        for name in button_names:
            button = self.buttons.get(name)  # Получаем кнопку по имени.
            if button:
                button.config(state="disabled")  # Отключаем кнопку.
               
    def keys(self):
        
        keys = list(self.buttons.keys())  # Получаем ключи кнопок в виде списка
        index = 1  # Индекс элемента, который хотим получить (например, второй элемент)

        if 0 <= index < len(keys):
            key = keys[index]  # Получаем ключ (название операции) по индексу.
            value = self.buttons[key]  # Получаем объект кнопки по ключу.
            return value  # Возвращаем объект кнопки.
        else:
            print("Индекс за пределами диапазона ключей")  # Если индекс находится за пределами доступных кнопок, выдаем сообщение.

        

    def create_widgets(self):
        # Создаем фрейм для параметров
        self.param_frame = tk.Frame(self.root)
        self.param_frame.grid(row=0, column=0, rowspan=len(self.parameters), columnspan=2)

        for i, (param_name, _) in enumerate(self.parameters.items()):
           label = tk.Label(self.param_frame, text=f"{param_name}:",height=4)  
           label.grid(row=i, column=0,rowspan=1)  
           entry = tk.Entry(self.param_frame) 
           param_info = self.parameters.get(param_name)
           entry.insert(0, param_info["default_value"])
           entry.grid(row=i, column=1,rowspan=1)  
           self.entries[param_name] = entry

        
        self.operation_frame = tk.Frame(self.root)
        self.operation_frame.grid(row=len(self.parameters), column=0,  columnspan=1)
        for operation_name, operation_info in self.operations.items():
             button = tk.Button(self.operation_frame, text=operation_info["description"], command=operation_info["function"], width=40,bg="green")  
             button.grid(row=list(self.operations.keys()).index(operation_name), column=0, columnspan=2) 

             self.buttons[operation_name] = button  # Добавляем кнопку в словарь с ключом - названием операции.
             self.disable_buttons(operation_name)  # Изначально отключаем кнопку операции.
        self.enable_buttons("set_parameters")  # Включаем кнопку операции "set_parameters".
        
        
        self.entry_EROR = tk.Entry(self.root, width=120)
        self.entry_EROR.insert(0,"Здесь будут сообщения об ошибках.\n" )
        self.entry_EROR.grid(row=len(self.parameters) , column=2) 
        
        if (self.Graph is not False):
           self.Graph_Frame = tk.Frame(self.root)
           self.Graph_Frame.grid(row=0, column=2, columnspan=2,rowspan=4)

           self.fig, self.ax = plt.subplots(figsize=(7.2, 3.6))
           self.canvas = FigureCanvasTkAgg(self.fig, master=self.Graph_Frame)
           self.canvas.get_tk_widget().grid(row=0, column=0, columnspan=2)

   # Создайте панель навигации внутри Graph_Frame
           nav_frame = tk.Frame(self.Graph_Frame)
           nav_frame.grid(row=1, column=0, columnspan=2)

           self.toolbar = NavigationToolbar2Tk(self.canvas, nav_frame)
           self.toolbar.update()
        
        if (self.Give_Text is not False):
            self.Text_frame = tk.Frame(self.root)
            self.Text_frame.grid(row=0, column=4, rowspan=6,columnspan=2)
            self.Give_Text = tk.Text(self.Text_frame, wrap=tk.WORD, width=12,height=30)
            self.Give_Text.grid(row=0, column=0,rowspan=5,columnspan=2)
            self.Give_Text_button= tk.Button(self.Text_frame, text="Считать данные ", command=self.Give_Text_reader)
            self.Give_Text_button.grid(row=5, column=0)
            self.Set_Text_button= tk.Button(self.Text_frame, text="Получить данные", command=self.save_to_text_widget,state="disabled")
            self.Set_Text_button.grid(row=6, column=0)
            


    def set_parameters(self):
       try:
            for param_name, entry in self.entries.items():
           
               value = entry.get()

               if param_name not in self.parameters:
                   raise ValueError(f"Параметр {param_name} не определен")

               param_info = self.parameters.get(param_name)
   
               if param_info is None:
                   raise ValueError(f"Информация о параметре {param_name} не найдена")

               param_type = param_info["type"]
               default_value = param_info["default_value"]

               if not value:
                   value = default_value

               if param_type == "int":
                   try:
                       value = int(value)
                   except ValueError:
                       raise ValueError(f"Недопустимое значение параметра {param_name}: {value} (ожидается int)")
               elif param_type == "float":
                   try:
                       value = float(value)
                   except ValueError:
                      raise ValueError(f"Недопустимое значение параметра {param_name}: {value} (ожидается float)")
               elif param_type == "Func":
                   try:
                       value = eval(value)
                   except:
                       raise ValueError(f"Недопустимое значение параметра {param_name}: {value} (ожидается функция)")
               else:
                   raise ValueError(f"Тип параметра не поддерживается для {param_name}: {param_type}")

               param_info["default_value"] = value
               

               self.enable_buttons("calculate_parametrs")
               
               self.entry_EROR.configure(bg="green")
               self.entry_EROR.delete(0, tk.END)
               self.entry_EROR.insert(0,"Всё верно" )
       except ValueError as e:
               self.entry_EROR.configure(bg="red")    
               self.entry_EROR.delete(0, tk.END)
               self.entry_EROR.insert(0,f"{e}" )
              

    def calculate_parametrs(self):
        if self.Func_name is False : 
          if "x0" in self.parameters:
            x0 = self.parameters["x0"]["default_value"]
          if "dx" in self.parameters:
            dx = self.parameters["dx"]["default_value"]
          if "N" in self.parameters:
            N = abs(self.parameters["N"]["default_value"])
          if "Function" in self.parameters:
            func = self.parameters["Function"]["default_value"]
          mas_x = self.matrix_instance.set_X(N, x0, dx)
          mas_y = self.matrix_instance.set_Y(func)
          self.matrix_instance.mas_coordinat(mas_x, mas_y)
          self.enable_buttons("draw_graph", "write_to_file", "read_from_file", "close_app")
          self.Set_Text_button.config(state="active")
        else:   
          try :
            result = self.Func_name(self.parameters)
            if result is not None:
               pass
            else:
               raise ValueError("Фукция обработки не возвращает значения , исправьте код функции обработки")
                
    # Функция вернула None
    
            self.matrix_instance=self.Func_name(self.parameters)
            
            self.entry_EROR.configure(bg="green")
            self.entry_EROR.delete(0, tk.END)
            self.entry_EROR.insert(0,"Всё верно" )
            self.enable_buttons("draw_graph", "write_to_file", "read_from_file", "close_app")
            self.Set_Text_button.config(state="active")
          except ValueError as e:
            self.entry_EROR.configure(bg="red")
            self.entry_EROR.delete(0, tk.END)
            self.entry_EROR.insert(0,f"{e}" )
        
        

        
        
    def Give_Text_reader(self):
        try:
            text = self.Give_Text.get("1.0", "end-1c")
            with open(self.Job_file, "w") as file:
                file.write(text)
            self.read_from_file
            mass=Matrix.Matrix()
            mass.set_File_data(self.Job_file)
            
            #Блок ax
            self.ax.set_title("График")
            self.ax.set_xlabel("X-ось")
            self.ax.set_ylabel("Y-ось")
            
            #Рисовать в Ax
            mass.plot(ax=self.ax, label='График', color='red', linestyle='--')
            
            self.canvas.draw()
            self.entry_EROR.configure(bg="green")
            self.entry_EROR.delete(0, tk.END)
            self.entry_EROR.insert(0,"Всё верно" )
        except ValueError as e:
            self.entry_EROR.configure(bg="red")
            self.entry_EROR.delete(0, tk.END)
            self.entry_EROR.insert(0,f"Ошибка при считывании файла" )
      
            
    def save_to_text_widget(self):
       self.matrix_instance.out(self.Job_file)
       self.load_file_to_text_widget()
           

    def load_file_to_text_widget(self):
       try:
          with open(self.Job_file, "r") as file:
            file_contents = file.read()
            self.Give_Text.delete("1.0", tk.END)  # Очищаем виджет Text
            self.Give_Text.insert("1.0", file_contents)  # Вставляем содержимое файла в виджет Text
       except Exception as e:
            print(f"Ошибка при загрузке файла: {e}")


    def draw_graph(self):
        
    # Очистите текущий график
        
        self.ax.set_title("График")
        self.ax.set_xlabel("X-ось")
        self.ax.set_ylabel("Y-ось")

        # Добавьте легенду, если у вас есть несколько графиков
        self.ax.legend()


    # Перерисовать график
        self.matrix_instance.plot(ax=self.ax, label='График', color='blue', linestyle='--')
    
    # Обновите виджет с новым графиком
        self.canvas.draw()
        self.enable_buttons('save_graph', 'clear_graph')
        self.entry_EROR.configure(bg="green")
        self.entry_EROR.delete(0, tk.END)
        self.entry_EROR.insert(0,"График построен" )

    def write_to_file(self):
        file_path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Text Files", "*.txt")])

# Если пользователь выбрал файл для сохранения
        if file_path:
          try:
       
             with open(file_path, "w") as file:
                  # Записываем в файл нужный текст (замените эту строку на ваш текст)
                  self.matrix_instance.out(file_path)
             self.entry_EROR.configure(bg="green")
             self.entry_EROR.delete(0, tk.END)
             self.entry_EROR.insert(0,f"Файл успешно сохранен по пути: {file_path}" )
          except Exception as e:
             self.entry_EROR.configure(bg="red")
             self.entry_EROR.delete(0, tk.END)
             self.entry_EROR.insert(0,f"Ошибка при сохранении файла: {e}" )
        else:
             self.entry_EROR.configure(bg="red") 
             self.entry_EROR.delete(0, tk.END)
             self.entry_EROR.insert(0,f"Сохранение отменено или файл не выбран" )

        

    def read_from_file(self):
        
        file = filedialog.askopenfile(filetypes=[("Text Files", "*.txt")])
        
        try:
           
              file_contents = file.read()
              self.Give_Text.delete("1.0", tk.END)  # Очищаем виджет Text
              self.Give_Text.insert("1.0", file_contents)  # Вставляем содержимое файла в виджет Text
        except Exception as e:
             print(f"Ошибка при загрузке файла: {e}")

    def save_graph(self):
        file_path = filedialog.asksaveasfilename(defaultextension=".png", filetypes=[("PNG Files", "*.png")])
        if file_path:
            self.fig.savefig(file_path)

    def clear_graph(self):
        self.ax.clear()
        self.ax.set_title("")
        self.fig.canvas.draw()

    def close_app(self):
        self.root.destroy()





