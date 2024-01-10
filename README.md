import tkinter as tk 
from tkinter import filedialog
from tkinter import messagebox

class Window:
    def __init__(self,window):
        self.window=window
        window.title("HEX EDITOR")
        window.geometry("1010x590")
        window.resizable(True,True)
        self.contentb = ""
        self.total_lines = 0
        self.scroll_level = 0
        self.widgets(window)

    # διαγραφή των χαρακτήρων που έχει εισάγει ο δέκτης πριν πατήσει το κουμπί open file 
    def delete_users_characters(self):
        self.print_contentx.delete("1.0", tk.END)           #διαγράφει απο τον 1ο χαρακτήρα (1.0) εως τον τελευταίο(END) στα Text Widgets 
        self.print_original_content.delete("1.0", tk.END)   
        self.lines_text.delete("1.0", tk.END)  

    def widgets(self,window):                              
        #ετικέτα με τίτλο "HEX EDITOR" μέσα στο παράθυρο 
        self.title_label = tk.Label(window, text="HEX EDITOR", font=("Helvetica", 16))
        self.title_label.grid(row=0,column=1,sticky=tk.N )

        #κουμπί "Open File" που όταν πατηθεί δίνει την δυνατότητα στον δέκτη να επιλέξει ποιό αρχείο θέλει να ανοίξει
        self.open_button = tk.Button(window, text="Open File", font=("Helvetica", 14), command=self.open_file, activebackground="#DE3131",bg="#CBCBCB")
        self.open_button.grid(row=1,column=0,sticky=tk.SW,padx=1)

        #κουμπι "Save" που όταν πατηθεί δίνει την δυνατότητα στον δέκτη να επιλέξει ποιό αρχείο θέλει να αντικαταστήσει ή που θέλει να δημιουργήσει νέο αρχείο αφού επεξεργάστηκε την δεκαεξαδική αναπαράσταση του αρχικού αρχείου
        self.save_button=tk.Button(window,text="SAVE",font=("Helvetica", 12),command=self.save_file,activebackground="#3CC032",bg="#CBCBCB")
        self.save_button.grid(row=1,column=0,sticky=tk.SE)

        #βοηθητική ετικέτα που δεν εμφανίζει κάποιο μήνυμα
        self.label=tk.Label(window).grid(row=2,column=0,columnspan=3)

        #ετικέτα που τοποθετείται ακριβώς πάνω απο το Text Widget στο οποίο τυπώνεται η δεκαεξαδική αναπαράσταση, και δηλώνει σε δεκαεξαδική μορφή της  16 στήλες της αναπαράστασης
        self.columns_label=tk.Label(window,text="00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F " , font=("Courier New", 16),fg="#0000FF")
        self.columns_label.grid(row=3,column=1)

        #κουμπί "V", που όταν πατηθεί γίνεται scroll προς τα κάτω κατά μια γραμμή (κατα 16 χαρακτήρες) σε όλα τα Text Widgets
        self.scroll_down_button=tk.Button(window,text="V",width=10,command=lambda: self.scroll(+16),activebackground="#DE3131",bg="#E0E0E0")
        self.scroll_down_button.grid(row=6,column=1,sticky=tk.N)
        
        #κουμπί "Λ", που όταν πατηθεί γίνεται scroll προς τα πάνω κατά μια γραμμή (κατα 16 χαρακτήρες) σε όλα τα Text Widgets
        self.scroll_up_button=tk.Button(window,text="Λ",width=10,command=lambda: self.scroll(-16),activebackground="#DE3131",bg="#E0E0E0")
        self.scroll_up_button.grid(row=5,column=1,sticky=tk.N)
        
        #κουμπί "Exit", που όταν πατηθεί "καταστρέφει" το παράθυρο/ "κλένει" η εφαρμογή
        self.exit_button = tk.Button(window, text="Exit",font=("Helvetica", 14),width=9, command=window.destroy,activebackground="#DE3131",bg="#CBCBCB")
        self.exit_button .grid(row=6,column=2,sticky=tk.E,padx=1)
        
        #Text Widget "lines_text", στο οποίο "τυπώνεται" σε δεκαεξαδική μορφή ο αριθμός κάθε γραμμής της δεκαεξαδικής αναπαράστασης
        self.lines_text = tk.Text(window, width=12, height=16, font=("Courier New", 16),fg="#0000FF")
        self.lines_text.grid(row=4, column=0, sticky=tk.SW,padx=1)
        
        #ετικέτα "total lines:", που τοποθετείται ακριβώς κάτω απο το Text Widget "lines_text" και εμφανίζει τον συνολικό αριθμό των γραμμών της δεκαεξαδικής αναπαράστασης
        self.lines_label = tk.Label(window, text="total lines:",font=("Courier New", 12))
        self.lines_label.grid(row=5, column=0, sticky=tk.NW)
        
        #Text Widget "print_contentx", στο οποίο "τυπώνεται" η δεκαεξαδική αναπαράσταση του περιεχομένου του επιλεγμένου αρχείου
        self.print_contentx = tk.Text(window, width=48, height=16, font=("Courier New", 16),highlightcolor="#DE3131",highlightbackground="#DE3131")
        self.print_contentx .grid(row=4, column=1)
        
        #Text Widget "print_original_content", στο οποίο "τυπώνεται" το περιεχόμενο του επιλεγμένου αρχείου κατα ASCII
        self.print_original_content = tk.Text(window, height=16, width=16, font=("Courier New", 16), bg="#E2E2E2")
        self.print_original_content.grid(row=4, column=2, padx=1, sticky=tk.NSEW)
    
        #κουμπί "refresh_button":Το πατάει ο χρήστης όταν επεξεργαστεί την δεκαεξαδική αναπαράσταση για να δει την αλλαγή που έγινε και στο αρχικό περιεχόμενο κατα ASCII 
        self.refresh_button=tk.Button(window,text="Refresh Preview",font=("Helvetica", 13),width=20,command=self.refresh,activebackground="#DE3131",bg="#E0E0E0")
        self.refresh_button.grid(row=1,column=2,sticky=tk.E,padx=1)
        

    def lines(self,i):        #επιστρέφει σε δεκαεξαδικό αριθμό και με ακρίβεια 10 ψηφίων τον αριθμό της γραμμής της δεκαεξαδικής αναπαράστασης 
        return ("%09x"%(i//16)).upper()+"0  "   # το i ειναι καθε φορα πολλαπλασιο του 16. 16,32,48,64,80,96,.. ή 1

    def print_Content(self,scroll_level):      #"τυπώνει" στα Text Widgets τις γραμμές, την δεκαεξαδική αναπαράσταση, την ASCII αναπαράσταση
        self.delete_users_characters() 
        self.lines_text.insert(tk.END, self.lines(scroll_level))  
        i=1                                             #οριζω ετσι το 1ο byte δλδ τον 1ο χαρακτηρα 
        characters_in_hex = ""                          #μια συμβολοσειρα που θα περιέχει ολο το περιεχόμενο του αρχείου σε δεκαεξαδική μορφή
        for character in self.contentb[0+scroll_level:256+scroll_level]:      # 256 = 16 στηλεσ*16 χαρακτηρεσ με scroll_level=+16 ή scroll_level=-16 
            characterx=("%02x"%character).upper()                        #μετατροπή κάθε χαρακτήρα σε δεκαεξαδικό 
            characters_in_hex += characterx + " "
            if i%16==0: self.lines_text.insert(tk.END,self.lines(i+scroll_level))     # κάθε 16 χαρακτήρεσ/bytes τυπ'ωνεται ο αριθμός της γραμμής του δεκαεξαδικού κειμενου 
            i+=1
        self.print_contentx.insert(tk.END, characters_in_hex)       
        
        ascii_content=""                                 
        #μια συμβολοσειρα που θα περιέχει ολο το περιεχόμενο του αρχείου κατά ASCII
        for character in self.contentb[0+scroll_level:256+scroll_level]:
            if character>=32 and character<=126: ascii_content+= chr(character)     #μετατροπή κάθε τυπώσιμου χαρακτήρα κατα ASCII
            else: ascii_content+= "."""                                             # οι μη-τυπώσιμοι χαρακτήρες εμφανίζονται με τελεια(.)
        for character in range(0,len(ascii_content),16):  #τύπωση 16 ascii χαρακτήρων σε κάθε γραμμή 
            x=ascii_content[character:character+16] + "\n"
            self.print_original_content.insert(tk.END,x)

    def scroll(self,i):                       # εαν i>0: γινεται scroll down κατα μια μονο γραμμη.προστηθεται στο οπτικο πεδιο του δεκτη μια ακομα γραμμη μεταφρασησ ενω αφαιρειται η πανω πανω 
        self.temp_save()                        # εαν i<0 γινεται scroll up κατα μια γραμμη ....
        if self.scroll_level != 0 or i > 0:
            self.scroll_level += i
            self.print_Content(self.scroll_level)
    
    def open_file(self):
        file = file = filedialog.askopenfilename()
        if file:
            self.scroll_level=0        # όταν ανοίξω ξανα αρχείο (χωρίς να έχω κλείσει την εφαρμογή) να γίνει reset η τιμή του scroll level
            file=open(file,"rb")
            self.contentb=contentb =bytearray(file.read())     #Μπορώ να τροποποιήσω τα στοιχεία ενός bytearray,αφού θεωρεί ότι το κάθε byte του αρχείου έιναι ανεξάρτητο/έχει την δικιά του θέση στην μεταβλητή contentb 
            self.total_lines = total_lines = self.lines(len(contentb))
            self.lines_label.config(text="total lines:\n" + total_lines)  
            self.delete_users_characters()
            self.lines_text.insert(tk.END, self.lines(1))           
            self.print_Content(0)
            file.close()

    def temp_save(self):   #γίνεται προσωρινή αποθήκευση #χρησιμεύει όταν ο χρήστης έχει επεξεργαστεί την δεκαεξαδική ανπαράσταση
        temp = self.print_contentx.get("1.0", tk.END).replace(" ","")
        try: 
            for i in range(0,len(temp)-1,2 ):
                character = temp[i]
                character += temp[i+1]        #δημιουργώ κάθε ζέυγος δεκαεξαδικού αριθμού απο την αντίστοιχη αναπαράσταση
                character = int(character, 16)
                self.contentb[(i//2)+self.scroll_level] = character      # στις ((i//2)+self.scroll_level) θέσεις της contentb αντικαθιστώ το κάθε ζεύγος( δλδ την character)
        except:      #ένδειξη μηνύματος σφάλματος κατά την επεξεργασία του δεκαεξαδικού κειμένου απο τον δέκτη 
            messagebox.showerror("Error", "A non-hexadecimal digit found.\nPlease enter only hexadecimal digits!")
    

    def refresh(self):
        self.temp_save()
        self.print_Content(self.scroll_level)

    def save_file(self):
        self.temp_save()
        self.save_changed_file_to = save_changed_file_to = filedialog.asksaveasfilename()
        if save_changed_file_to:
            try: 
                new_content_in_binary =self.contentb
                new_file=open(save_changed_file_to,"wb")
                new_file.write(new_content_in_binary)
            except:      #ένδειξη μηνύματος σφάλματος κατά την επεξεργασία του δεκαεξαδικού κειμένου απο τον δέκτη 
                messagebox.showerror("Error", "A non-hexadecimal digit found.\nPlease enter only hexadecimal digits!")
    
if __name__ == "__main__": #κυριως προγραμμα
    root=tk.Tk()
    Window(root)
    root.mainloop()
