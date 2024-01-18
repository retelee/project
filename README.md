import customtkinter as ctk
from tkinter import messagebox, Listbox, END, Toplevel, Label, Button
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

class ToDoApp(ctk.CTk):
    def __init__(self):
        self.successful_tasks = 0
        self.total_tasks = 0

        super().__init__()

        self.title("할일 목록 관리 앱")
        self.minsize(400, 300)

        self.entry = ctk.CTkEntry(master=self)
        self.entry.pack(pady=10)

        self.add_button = ctk.CTkButton(master=self, command=self.add_task, text="추가")
        self.add_button.pack(pady=5)

        self.delete_button = ctk.CTkButton(master=self, command=self.delete_task, text="삭제", fg_color=("orange", "darkorange"), hover_color=("red", "darkred"))
        self.delete_button.pack(pady=5)

        self.success_rate_button = ctk.CTkButton(master=self, command=self.show_success_rate, text="성공률 확인")
        self.success_rate_button.pack(pady=5)

        self.listbox = Listbox(master=self, width=40, height=10)
        self.listbox.pack(pady=10)

    def add_task(self):
        task = self.entry.get()
        if task:
            self.listbox.insert(END, task)
            self.entry.delete(0, END)
        else:
            messagebox.showwarning("경고", "할일을 입력하세요.")

    def delete_task(self):
        try:
            selected_task_index = self.listbox.curselection()[0]
            selected_task = self.listbox.get(selected_task_index)

            self.total_tasks += 1

            confirmation = messagebox.askquestion("확인", f"'{selected_task}'을(를) 성공하셨습니까?")
            if confirmation == 'yes':
                self.listbox.delete(selected_task_index)
                self.successful_tasks += 1
            elif confirmation == 'no':
                self.listbox.delete(selected_task_index)

        except IndexError:
            messagebox.showwarning("경고", "삭제할 항목을 선택하세요.")

    def show_success_rate(self):
        success_rate_window = Toplevel(self)
        success_rate_window.title("성공률 확인")

        if self.total_tasks == 0:
            success_rate_label = Label(success_rate_window, text="아직 성공률을 계산할 할일이 없습니다.")
        else:
            success_rate_percentage = (self.successful_tasks / self.total_tasks) * 100
            success_rate_label = Label(success_rate_window, text=f"성공률: {success_rate_percentage:.2f}%")
            self.fig, self.ax = plt.subplots()
            self.canvas = FigureCanvasTkAgg(self.fig, master=success_rate_window)
            self.canvas.get_tk_widget().pack(pady=10)
            self.ax.clear()
            self.ax.bar(['true', 'false'], [self.successful_tasks, self.total_tasks - self.successful_tasks], color=['green', 'red'])
            self.ax.set_ylabel('count')
            self.ax.set_title('success_rate')

            self.canvas.draw()

        success_rate_label.pack(pady=10)

        close_button = Button(success_rate_window, text="닫기", command=success_rate_window.destroy)
        close_button.pack(pady=5)

if __name__ == "__main__":
    app = ToDoApp()
    app.mainloop()
