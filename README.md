로그인 화면, 목표 양 입력, 칼로리 포함 식사 도움말, 로그아웃 기능 
추가해야 될 기능 : 






























import tkinter as tk
from tkinter import messagebox, simpledialog, Toplevel, Label

# 로그인 창 클래스
class LoginFrame(tk.Frame):
    def __init__(self, master, login_success_callback):
        super().__init__(master)
        self.login_success_callback = login_success_callback
        tk.Label(self, text="아이디:").pack()
        self.username_entry = tk.Entry(self)
        self.username_entry.pack()
        tk.Label(self, text="비밀번호:").pack()
        self.password_entry = tk.Entry(self, show="*")
        self.password_entry.pack()
        tk.Button(self, text="로그인", command=self.login).pack()

    def login(self):
        # 여기서는 실제 인증 과정을 생략하고, 입력값을 확인하는 간단한 예제로 구현합니다.
        username = self.username_entry.get()
        password = self.password_entry.get()
        # 실제 애플리케이션에서는 사용자 인증 절차가 필요합니다.
        if username and password:  # 아이디와 비밀번호가 모두 입력됐다고 가정
            self.login_success_callback()  # 로그인 성공 콜백 함수 호출
        else:
            messagebox.showerror("로그인 실패", "아이디와 비밀번호를 모두 입력해주세요.")

class GoalSettingFrame(tk.Frame):
    def __init__(self, master, update_callback):
        super().__init__(master)
        self.update_callback = update_callback
        # 기존 영양소 목표 입력칸
        tk.Label(self, text="칼로리 목표:").pack(side="left")
        self.calorie_goal_entry = tk.Entry(self, width=10)
        self.calorie_goal_entry.pack(side="left")
        tk.Label(self, text="단백질 목표(g):").pack(side="left")
        self.protein_goal_entry = tk.Entry(self, width=10)
        self.protein_goal_entry.pack(side="left")
        tk.Label(self, text="지방 목표(g):").pack(side="left")
        self.fat_goal_entry = tk.Entry(self, width=10)
        self.fat_goal_entry.pack(side="left")
        
        # 탄수화물 목표 입력칸 추가
        tk.Label(self, text="탄수화물 목표(g):").pack(side="left")
        self.carb_goal_entry = tk.Entry(self, width=10)
        self.carb_goal_entry.pack(side="left")
        
        # 무기질 목표 입력칸 추가
        tk.Label(self, text="무기질 목표(mg):").pack(side="left")
        self.mineral_goal_entry = tk.Entry(self, width=10)
        self.mineral_goal_entry.pack(side="left")
        
        # 비타민 목표 입력칸 추가
        tk.Label(self, text="비타민 목표(mg):").pack(side="left")
        self.vitamin_goal_entry = tk.Entry(self, width=10)
        self.vitamin_goal_entry.pack(side="left")
        
        tk.Button(self, text="목표 설정", command=self.set_goals).pack(side="left")

    def set_goals(self):
        # 새로운 목표 값들을 가져옵니다.
        calorie_goal = self.calorie_goal_entry.get()
        protein_goal = self.protein_goal_entry.get()
        fat_goal = self.fat_goal_entry.get()
        carb_goal = self.carb_goal_entry.get()
        mineral_goal = self.mineral_goal_entry.get()
        vitamin_goal = self.vitamin_goal_entry.get()
        
        # 새로운 목표 값들을 update_callback 함수에 전달합니다.
        self.update_callback(calorie_goal, protein_goal, fat_goal, carb_goal, mineral_goal, vitamin_goal)

# 상태 업데이트 로직 수정

# 상태 업데이트 클래스
class StatusFrame(tk.Frame):
    def __init__(self, master):
        super().__init__(master)
        self.status_label = tk.Label(self, text="상태: 목표를 설정해주세요.")
        self.status_label.pack()

    def update_status(self, status_text):
        # 입력받은 모든 목표 값을 화면에 표시합니다.
        self.status_label.config(text=status_text)

# 식사 목록 관리 클래스
class MealListFrame(tk.Frame):
    def __init__(self, master):
        super().__init__(master)
        self.meals = []  # 식사 정보를 담을 리스트 (식사 이름, 칼로리)
        self.meal_listbox = tk.Listbox(self)
        self.meal_listbox.pack(side="left", fill="both", expand=True)
        tk.Button(self, text="식사 삭제", command=self.remove_meal).pack(side="left")

    def add_meal(self, meal_info):
        self.meals.append(meal_info)
        display_text = f"{meal_info[0]}: {meal_info[1]}kcal"  # 식사 이름: 칼로리 형식
        self.meal_listbox.insert(tk.END, display_text)

    def remove_meal(self):
        selected = self.meal_listbox.curselection()
        if selected:
            self.meal_listbox.delete(selected[0])
            del self.meals[selected[0]]

# 식사 관리 클래스
class MealManagementFrame(tk.Frame):
    def __init__(self, master, meal_list_frame):
        super().__init__(master)
        self.meal_list_frame = meal_list_frame
        tk.Button(self, text="식사 추가", command=self.add_meal).pack()

    def add_meal(self):
        while True:
            meal_name = simpledialog.askstring("식사 추가", "식사 이름을 입력하세요:", parent=self.master)
            if meal_name is None:  # 사용자가 Cancel 버튼을 누른 경우
                return  # 함수 종료
            elif not meal_name:  # 식사 이름이 비어있는 경우
                messagebox.showerror("입력 오류", "식사 이름을 입력해주세요.")
                continue  # 다시 입력 창을 띄움
            break  # 유효한 입력을 받으면 루프 탈출
        
        while True:
            calorie = simpledialog.askstring("칼로리 추가", "칼로리(kcal)를 입력하세요:", parent=self.master)
            if calorie is None:  # 사용자가 Cancel 버튼을 누른 경우
                return  # 함수 종료
            elif not calorie:  # 칼로리가 비어있는 경우
                messagebox.showerror("입력 오류", "칼로리를 입력해주세요.")
                continue  # 다시 입력 창을 띄움
            break  # 유효한 입력을 받으면 루프 탈출
        
        # 식사 정보를 목록에 추가
        self.meal_list_frame.add_meal((meal_name, calorie))



class MainApp(tk.Tk):
    def __init__(self):
        # 기존 초기화 코드...
        super().__init__()
        self.title("로그인")
        self.geometry("300x200")  # 로그인 창의 초기 크기 설정
        self.login_frame = LoginFrame(self, self.show_main_window)
        self.login_frame.pack()

    def show_main_window(self):
        # 기존 메인 창 설정 코드...
        self.login_frame.pack_forget()
        self.title("식사 계획 및 영양 정보 관리자")
        self.geometry("1100x500")  # 메인 창의 크기 설정

        # 로그아웃 버튼 생성 및 배치
        logout_button = tk.Button(self, text="로그아웃", command=self.logout)
        logout_button.pack(pady=20)


        self.goal_frame = GoalSettingFrame(self, self.update_status)
        self.goal_frame.pack(pady=10)
        self.status_frame = StatusFrame(self)
        self.status_frame.pack(pady=10)
        self.meal_list_frame = MealListFrame(self)
        self.meal_list_frame.pack(pady=10)
        self.meal_management_frame = MealManagementFrame(self, self.meal_list_frame)
        self.meal_management_frame.pack(pady=10)



# 도움말 버튼 생성 및 배치
        help_button = tk.Button(self, text="도움말", command=self.open_help_window)

        help_button.pack(pady=20)

    def logout(self):
        # 현재 메인 화면의 모든 컴포넌트를 숨김
        for widget in self.winfo_children():
            widget.pack_forget()
        
        # 로그인 화면을 다시 초기화하고 표시
        self.login_frame = LoginFrame(self, self.show_main_window)
        self.login_frame.pack()
        
        # 로그인 창으로 제목과 크기 조정
        self.title("로그인")
        self.geometry("300x200")

    def update_status(self, calorie_goal, protein_goal, fat_goal, carb_goal, mineral_goal, vitamin_goal):
        status_text = f"설정된 목표 - 칼로리: {calorie_goal}, 단백질: {protein_goal}, 지방: {fat_goal}, 탄수화물: {carb_goal}, 무기질: {mineral_goal}, 비타민: {vitamin_goal}"
        self.status_frame.update_status(status_text)


    def open_help_window(self):
        # HelpWindow 클래스 인스턴스 생성
        self.help_window = HelpWindow(self)




class HelpWindow:
    def __init__(self, parent):
        # 도움말 창 생성 및 기본 설정
        self.window = tk.Toplevel(parent)
        self.window.title("도움말")
        self.window.geometry("500x200")

        # 도움말 정보 표시
        help_text = "식사 관리 프로그램 사용법:\n\n" \
                    "- 식사 추가: '추가' 버튼을 클릭하여 식사를 추가합니다.\n" \
                    "- 식사 삭제: 목록에서 식사를 선택 후 '삭제' 버튼을 클릭합니다.\n" \
                    "- 영양 정보 입력: 각 식사의 영양 정보를 입력할 수 있습니다.\n" \
                    "\n프로그램을 사용해 주셔서 감사합니다!"
        self.label = tk.Label(self.window, text=help_text, justify=tk.LEFT)
        self.label.pack(padx=10, pady=10)


if __name__ == "__main__":
    app = MainApp()
    app.mainloop()
