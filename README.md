import tkinter as tk
import pygame
import PIL
from PIL import ImageTk,Image
rows = 10
cols = 10
mines = 10
field = []
buttons = []
colors = ['#FFFFFF', '#0000FF', '#008200', '#FF0000', '#000084', '#840000', '#008284', '#840084', '#000000']
gameover = False
customsizes =[]
def start_game():
        app.destroy()
        
        import tkinter , random, tkinter.messagebox, tkinter.simpledialog   
        window = tkinter.Tk()
        window.title("Minesweeper")
        window.geometry("600x750")
        def createMenu():
            menubar = tkinter.Menu(window)
            menusize = tkinter.Menu(window, tearoff=0)
            menusize.add_command(label="small (10x10 with 10 mines)", command= lambda: setSize(10, 10, 15))
            menusize.add_command(label="medium (20x20 with 40 mines)", command=lambda: setSize(16, 16, 50))
            menusize.add_command(label="big (25x25 with 90 mines)", command= setSize(25, 25, 90))
            menusize.add_command(label="custom", command=setCustomSize)
            
            for x in range(0, len(customsizes)):
                 menusize.add_command(label=str(customsizes[x][0])+"x"+str(customsizes[x][1])+" with "+str(customsizes[x][2])+" mines", command=lambda customsizes=customsizes: setSize(customsizes[x][0], customsizes[x][1], customsizes[x][2]))
            menubar.add_cascade(label="size", menu=menusize)
            menubar.add_command(label="exit", command=lambda: window.destroy())
            window.config(menu=menubar)
        def setCustomSize():
            global customsizes
            r = tkinter.simpledialog.askinteger("Custom size", "Enter amount of rows")
            c = tkinter.simpledialog.askinteger("Custom size", "Enter amount of columns")
            m = tkinter.simpledialog.askinteger("Custom size", "Enter amount of mines")
            while m > r*c:
             m = tkinter.simpledialog.askinteger("Custom size", "Maximum mines for this dimension is: " + str(r*c) + "\nEnter amount of mines")
            customsizes.insert(0, (r,c,m))
            customsizes = customsizes[0:5]
            setSize(r,c,m)
            createMenu()
        def setSize(r,c,m):
            global rows, cols, mines
            rows = r
            cols = c
            mines = m
            restartGame()     
        def prepareGame():
            global rows, cols, mines, field
            field = []
            for x in range(0, rows):
                field.append([])
                for y in range(0, cols):
                #add button and init value for game
                 field[x].append(0)
        #generating mines
            for _ in range(0, mines):
                 x = random.randint(0, rows-1)
                 y = random.randint(0, cols-1)
                 while field[x][y]==-1:
                     x = random.randint(0, rows-1)
                     y = random.randint(0, cols-1)
                 field[x][y] = -1
                 if x != 0:
                   if y != 0:
                    if field[x-1][y-1] != -1:
                        field[x-1][y-1] = int(field[x-1][y-1]) + 1
                   if field[x-1][y] != -1:
                        field[x-1][y] = int(field[x-1][y]) + 1
                   if y != cols-1:
                    if field[x-1][y+1] != -1:
                        field[x-1][y+1] = int(field[x-1][y+1]) + 1
                 if y != 0:
                    if field[x][y-1] != -1:
                        field[x][y-1] = int(field[x][y-1]) + 1
                 if y != cols-1:
                    if field[x][y+1] != -1:
                        field[x][y+1] = int(field[x][y+1]) + 1
                 if x != rows-1:
                     if y != 0:
                      if field[x+1][y-1] != -1:
                        field[x+1][y-1] = int(field[x+1][y-1]) + 1
                     if field[x+1][y] != -1:
                        field[x+1][y] = int(field[x+1][y]) + 1
                     if y != cols-1:
                       if field[x+1][y+1] != -1:
                         field[x+1][y+1] = int(field[x+1][y+1]) + 1
        def prepareWindow():
            global rows, cols, buttons
            tk.Button(window, text="Restart",font=("bold",12), command=restartGame).grid(row=0, column=0, columnspan=cols, sticky=tk.N+tk.W+tk.S+tk.E)
            buttons = []
            for x in range(0, rows):
                buttons.append([])
                for y in range(0, cols):
                    b = tk.Button(window, text=" ", width=2, command=lambda x=x,y=y: clickOn(x,y))
                    b.bind("<Button-3>", lambda e, x=x, y=y:onRightClick(x, y))
                    b.grid(row=x+1, column=y, sticky=tk.N+tk.W+tk.S+tk.E)
                    buttons[x].append(b)

        def restartGame():
            global gameover
            gameover = False
            for x in window.winfo_children():
                if type(x) != tkinter.Menu:
                    x.destroy()
            prepareWindow()
            prepareGame()

    # Initializing pygame mixer for sound effects
        pygame.mixer.init()
    #  sound effects
        click_sound = pygame.mixer.Sound("C:\\Users\\LENOVO\\Downloads\\click (mp3cut.net).wav")  # Replace "click.wav" with the path to your click sound effect
        strike_sound = pygame.mixer.Sound("C:\\Users\\LENOVO\\Downloads\\lose_minesweeper (mp3cut.net) (1).wav")  # Replace "strike.wav" with the path to your mine strike sound effect
        victory_sound = pygame.mixer.Sound("C:\\Users\\LENOVO\\Music\\win (1).wav")  # Replace "victory.wav" with the path to your victory sound effect
        def clickOn(x, y):
            global field, buttons, colors, gameover, rows, cols
            if gameover:
             return
            buttons[x][y]["text"] = str(field[x][y])
            if field[x][y] == -1:
                buttons[x][y]["text"] = "M"
                buttons[x][y].config(background='red', disabledforeground='black')
                strike_sound.play()
          
                gameover = True
                ''' tkinter.messagebox.showinfo("Game Over", "You have lost.")'''
            #now show all other mines
                for _x in range(0, rows):
                    for _y in range(cols):
                        if field[_x][_y] == -1:
                            buttons[_x][_y]["text"] = "M"
                            buttons[_x][_y].config(text="M",bg="red")
                            strike_sound.play()
                
                tkinter.messagebox.showinfo("Game Over", "You have lost.")
                
            else:
                buttons[x][y].config(bg="Aqua",disabledforeground=colors[field[x][y]])
            if field[x][y] == 0:
                buttons[x][y]["text"] = " "
                buttons[x][y].config(bg="DarkOliveGreen2")
            #now repeat for all buttons nearby which are 0... kek
                autoClickOn(x,y)
            buttons[x][y]['state'] = 'disabled'
            buttons[x][y].config(relief=tk.SUNKEN)
            checkWin()
            if field[x][y] == -1:
            # ... (The rest of the code remains the same)
                strike_sound.play()  # Play mine strike sound
            else:
            # ... (The rest of the code remains the same)
                click_sound.play()  # Play click sound

        def autoClickOn(x,y):
            global field, buttons, colors, rows, cols
            if buttons[x][y]["state"] == "disabled":
                buttons[x][y].config(bg="DarkOliveGreen2")
                return
            if field[x][y] != 0:
                buttons[x][y]["text"] = str(field[x][y])
            else:
                buttons[x][y]["text"] = " "
            buttons[x][y].config(disabledforeground=colors[field[x][y]])
            buttons[x][y].config(relief=tk.SUNKEN)
            buttons[x][y]['state'] = 'disabled'
            if field[x][y] == 0:
                if x != 0 and y != 0:
                    autoClickOn(x-1,y-1)
                if x != 0:
                    autoClickOn(x-1,y)
                if x != 0 and y != cols-1:
                    autoClickOn(x-1,y+1)
                if y != 0:
                    autoClickOn(x,y-1)
                if y != cols-1:
                    autoClickOn(x,y+1)
                if x != rows-1 and y != 0:
                    autoClickOn(x+1,y-1)
                if x != rows-1:
                    autoClickOn(x+1,y)
                if x != rows-1 and y != cols-1:
                    autoClickOn(x+1,y+1)
        def onRightClick(x,y):
            global buttons
            if gameover:
                return
            if buttons[x][y]["text"] == "?":
                buttons[x][y]["text"] = " "
                buttons[x][y]["state"] = "normal"
            elif buttons[x][y]["text"] == " " and buttons[x][y]["state"] == "normal":
                buttons[x][y].config(text="?",bg="grey")
                buttons[x][y]["state"] = "disabled"
        def checkWin():
            global buttons, field, rows, cols
            win = True
            for x in range(0, rows):
                for y in range(0, cols):
                    if field[x][y] != -1 and buttons[x][y]["state"] == "normal":
                        win = False
            if win:
                tkinter.messagebox.showinfo("Gave Over", "You have won.")
            if win:
                victory_sound.play()  # Play victory sound
                tkinter.messagebox.showinfo("Game Over", "You have won.")      
        createMenu()
        prepareWindow()
        prepareGame()
        window.mainloop()       
import tkinter as tk
from tkinter.constants import *
from tkinter import ttk
import PIL
from PIL import ImageTk,Image
class GameApp(tk.Tk):      
    def _init_(self, *args, **kwargs):
        super()._init_(*args, **kwargs)
        self.geometry("700x700")
        main_frame = tk.Frame(self)
        main_frame.pack(side='top', fill='both', expand='True')
        main_frame.grid_rowconfigure(0, weight=3)
        main_frame.grid_columnconfigure(0, weight=3)
        self.filename = ImageTk.PhotoImage(Image.open("C:\\Users\\LENOVO\\Downloads\\WhatsApp Image 2023-08-06 at 20.00.27.jpeg"))
        self.filename1= ImageTk.PhotoImage(Image.open("C:\\Users\\LENOVO\\Downloads\\WhatsApp Image 2023-08-06 at 20.21.35.jpeg"))
        self.filename2=ImageTk.PhotoImage(Image.open("C:\\Users\\LENOVO\\Downloads\\WhatsApp Image 2023-08-06 at 20.38.49.jpeg"))
        self.frames = {}
        for F in (HomePage, PageOne, PageTwo):
            frame = F(main_frame, self)
            self.frames[F] = frame
            frame.grid(row=0, column=0, sticky='nsew')
        self.show_frame(HomePage)
    def show_frame(self,container):
        frame = self.frames[container]
        frame.tkraise()
class BasePage(tk.Frame):
    def _init_(self, parent,controller):
        super()._init_(parent)

        label_bkgr = tk.Label(self, image=controller.filename)
        label_bkgr.place(relx=0.5, rely=0.5, anchor=CENTER)  # Center label w/image.
class BasePage1(tk.Frame):
    def _init_(self,parent,controller):
      super()._init_(parent)
      label_filename1 = tk.Label(self, image=controller.filename1)
      label_filename1.place(relx=0.5,rely=0.5,anchor="center")  # Center label w/image.
class BasePage2(tk.Frame):
    def _init_(self,parent,controller):
      super()._init_(parent)
      label_filename1 = tk.Label(self, image=controller.filename2)
      label_filename1.place(relx=0.5, rely=0.5, anchor=CENTER)
class HomePage(BasePage):
    def _init_(self, parent, controller):
        super()._init_(parent,controller)
        '''label = ttk.Label(self, text='WELCOME TO MINESWEEPER GAME ', font =("Helvetica",20))
        label.pack(padx=10, pady=10)'''
        button1 = ttk.Button(self, text="Play",
                             command=lambda: controller.show_frame(PageOne))
        button1.place(x=300,y=550)
        button6 = ttk.Button(self, text="Exit",
                             command=lambda: controller.show_frame(PageTwo))
        button6.place(x=300,y=600)
        pygame.mixer.init()
        bg_sound=pygame.mixer.Sound("C:\\Users\\LENOVO\\Downloads\\DRIVE(1) (mp3cut.net).wav")
        bg_sound.play()
class PageOne(BasePage1):
    def _init_(self,parent,controller):
        super()._init_(parent, controller)
        ''' label1 = ttk.Label(self, text='GET READY TO START THE GAME', font=("bold", 20))
        label1.pack(padx=10, pady=10)'''
        ''' lbl1=tk.Label(self,text="INSTRUCTIONS",font=("Serif",20))
        lbl1.place(x=100,y=100)
        lbl2=tk.Label(self,text="1.click on the cell to reveal.\n 2.Avoid clicking on the mines.\n 3.Use the numbers to identify the mines location.",bg="grey",
                      font=("Arial,18"))
        lbl2.place(x=90,y=200)'''
        button2 = ttk.Button(self, text="START",
                             command=start_game)
        button2.place(x=300,y=500)
        button5 = ttk.Button(self, text="BACK",
                             command=lambda: controller.show_frame(HomePage))
        button5.place(x=100,y=600)
class PageTwo(BasePage2):
    def _init_(self, parent,controller):
        super()._init_(parent,controller)
        label2 = tk.Label(self, text='Thank You For Opening', font=("bold", 20,"bold"))
        label2.place(x=150,y=300)       
app =GameApp()
app.mainloop()   
