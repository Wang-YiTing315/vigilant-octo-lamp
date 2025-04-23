# vigilant-octo-lampimport turtle
import random

win = turtle.Screen()
win.title("Snake Game") 
win.bgcolor("white") 
win.setup(width=560, height=620)  
win.tracer(0) 

snake=[] 

paused = False
game_over = False

border_pen = turtle.Turtle()
border_pen.speed(0)
border_pen.hideturtle()
border_pen.color("black")
border_pen.pensize(3)
    
border_pen.penup()
border_pen.goto(-250, -280)
border_pen.pendown()
border_pen.pensize(3)
    
for _ in range(4):   
    border_pen.forward(500)
    border_pen.left(90)
    
border_pen.penup()  
border_pen.goto(-250, 220)  # 左上角起点
border_pen.pendown()
border_pen.left(90)
border_pen.forward(60)
border_pen.right(90)
border_pen.forward(500)
border_pen.right(90)
border_pen.forward(60)

direction = "stop" 
status_bar = turtle.Turtle()
status_bar.hideturtle()
status_bar.penup()
status_bar.color("black")
status_bar.goto(-170, 240)
status_bar.write("Motion: ", align="center", font=("Arial", 18, "normal"))

game_time = 0
time_text = turtle.Turtle()
time_text.hideturtle()
time_text.penup()
time_text.color("black")
time_text.goto(0, 240)
time_text.write("Time: 0s", align="center", font=("Arial", 18, "normal"))

game_overlap_times=0
overlap_times = turtle.Turtle()
overlap_times.hideturtle()
overlap_times.penup()
overlap_times.color("black")
overlap_times.goto(170, 240)
overlap_times.write("Contact: 0 ", align="center", font=("Arial", 18, "normal"))

def update_overlap_times():
    global game_overlap_times
    if not game_over and not paused:
        for i in range(1,len(snake)):
            for j in range(4):
                if i < len(snake) and j < len(monsters):
                    if snake[i].distance(monsters[j]) < 20:  
                        game_overlap_times+=1

    overlap_times.clear()
    overlap_times.write("Contact: " + str(game_overlap_times) , align="center", font=("Arial", 12, "normal"))

    win.ontimer(update_overlap_times, random_time) 

def update_time():
    global game_time
    if not game_over:
        game_time += 1

    time_text.clear()
    time_text.write("Time: " + str(game_time) + "s", align="center", font=("Arial", 12, "normal"))

    win.ontimer(update_time, 1000) 

update_time()

def create_tail():
    tail = turtle.Turtle() 
    tail.speed(0) 
    tail.shape("square") 
    tail.color("blue", "black") 
    tail.penup() 
    if len(snake) > 0:
        tail.goto(snake[-1].xcor(), snake[-1].ycor())
    else:
        tail.goto(0, -30) 
    snake.append(tail) 

for _ in range(5):
    tail = create_tail()

head = turtle.Turtle()  # 创建蛇的头部
head.speed(0)  # 设置头部移动速度为最快
head.shape("square")  # 设置头部形状为正方形
head.color("red")  # 设置头部颜色为红色
head.penup()  # 抬起画笔，不绘制轨迹
head.goto(0, -30)  # 将头部定位在运动区中心
snake.insert(0, head)  # 将头部插入到蛇的列表的开头

def create_food(number):
    food = turtle.Turtle()
    food.speed(0)
    food.shape("square")
    food.color("black")
    food.penup()
    food.hideturtle()
    
    x = random.randint(0, 24)*20-240
    y = random.randint(0, 24)*20-46-240

    food.goto(x,y)  
    food.write(number, align="center", font=("Arial", 18, "bold"))

    text_x, text_y = food.position()
    text_offset = 16
    food.goto(text_x, text_y + text_offset)
    
    return food

foods = [] 
def create_monster():
    monster = turtle.Turtle()
    monster.speed(0)
    monster.shape("square")
    monster.color("purple")
    monster.penup()
    
    x = random.choice([random.randint(-240, 100),random.randint(100, 240)])
    y = random.choice([random.randint(-270, -130),random.randint(70, 210)])

    monster.goto(x, y)

    return monster

monsters = []

def update_food():
    global velocity_change
    
    for food in foods:
        if food.distance(snake[0]) < 20:
            food.clear() 
            velocity_change=25
            food_number=int(food.my_value)
            for _ in range(food_number):
                tail = create_tail()
            foods.remove(food) 

def encounter_monster():
    global game_over
    
    for monster in monsters:
        if monster.distance(snake[0]) < 20:
            game_over = True

            loser_message = turtle.Turtle() 
            loser_message.hideturtle()
            loser_message.penup()
            loser_message.color("black")
            loser_message.goto(0, -30)
            loser_message.write("Game Over !!", align="center", font=("Arial", 24, "bold"))
        
def move_food():
    for food in foods:
        if random.random() < 0.75:  
            direction = random.choice(["up", "down", "left", "right"])
            distance = 40

            x = food.xcor()
            y = food.ycor()-16
                
            if direction == "up":
                y += distance
            elif direction == "down":
                y -= distance
            elif direction == "left":
                x -= distance
            elif direction == "right":
                x += distance

            if -240 <= x <= 240 and -286 <= y <= 194:
                food.clear()
                food.goto(x, y)
                food_number=int(food.my_value)
                food.write(food_number, align="center", font=("Arial", 18, "bold"))

                text_x, text_y = food.position()
                text_offset = 16
                food.goto(text_x, text_y + text_offset)

def random_move_food():
    if not game_over:
        move_food()
        random_time = random.randint(5, 10) 
        turtle.ontimer(random_move_food, random_time * 1000) 

random_move_food()

def move_monsters():
    for monster in monsters:
        if random.random() < 0.25: 
            x = monster.xcor()
            y = monster.ycor()

            dx = snake[0].xcor() - x
            dy = snake[0].ycor() - y

            if abs(dx) > abs(dy): 
                if dx > 0:
                    x += 20
                else:
                    x -= 20
            else:
                if dy > 0:
                    y += 20
                else:
                    y -= 20

            monster.goto(x, y)

def move():
    global direction,status_bar
    if not paused and not game_over:
        x = snake[0].xcor() 
        y = snake[0].ycor()

        if (x < -230 and direction=="left") or (x > 230 and direction=="right") or (y < -260 and direction=="down") or (y > 200 and direction=="up"):
            direction = "stop" 

        if direction != "stop":
            for i in range(len(snake)-1, 0, -1):
                snake[i].goto(snake[i-1].xcor(), snake[i-1].ycor())

        if direction == "up":
            y += 20
        if direction == "down":
            y -= 20
        if direction == "left":
            x -= 20
        if direction == "right":
            x += 20

        snake[0].goto(x, y) 

        check_game()

        update_food()

        status_bar.clear()
        status_bar.write("Motion: " + direction.upper(), align="center", font=("Arial", 12, "normal"))

def go_up():
    global direction,paused
    if not paused:
        direction = "up"
    else:
        direction = "up"
        paused = False

def go_down():
    global direction,paused
    if not paused:
        direction = "down"
    else:
        direction = "down"
        paused = False

def go_left():
    global direction,paused
    if not paused:
        direction = "left"
    else:
        direction = "left"
        paused = False

def go_right():
    global direction,paused
    if not paused:
        direction = "right"
    else:
        direction = "right"
        paused = False

def toggle_pause():
    global paused
    paused = not paused

def check_game():
    global game_over
    
    encounter_monster() 
    
    for i in range(1,len(snake)):
        if snake[i-1].distance(snake[i]) != 20:
            return
        
    if len(foods)!=0 or len(snake)!=21 :
        return
            
    game_over=True

    winner_message = turtle.Turtle()
    winner_message.hideturtle()
    winner_message.penup()
    winner_message.color("black")
    winner_message.goto(0, -30)
    winner_message.write("Winner !!", align="center", font=("Arial", 24, "bold"))

start_message = turtle.Turtle()
start_message.hideturtle()
start_message.penup()
start_message.color("black")
start_message.goto(0, 0)
start_message.write("Click anywhere to start, have fun!!!!", align="center", font=("Arial", 16, "normal"))

def start_game(x, y):
    global food, start_message
    win.onclick(None) 
    start_message.clear()
    
    for i in range(1, 6): 
        food = create_food(i)
        food.my_value = i
        foods.append(food)

    for j in range(4):
        monster = create_monster()
        monsters.append(monster)

    win.listen()

win.onclick(start_game)

win.onkey(go_up, "Up")
win.onkey(go_down, "Down")
win.onkey(go_left, "Left") 
win.onkey(go_right, "Right")
win.onkey(toggle_pause, "space")

velocity_change=0 

def update_snake_movement():
    global velocity_change
    move() 
    win.update() 
    if not game_over:
        if snake[-1].distance(snake[-2]) != 0:
            velocity_change=0
        turtle.ontimer(update_snake_movement, 175+velocity_change) 

random_time=0 
def update_monster_movement():
    global random_time
    move_monsters()
    win.update()
    if not game_over :
        random_time=random.randint(150,200)
        turtle.ontimer(update_monster_movement, random_time)

update_snake_movement()
update_monster_movement()

update_overlap_times()

turtle.done()
