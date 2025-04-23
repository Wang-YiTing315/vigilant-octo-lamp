# vigilant-octo-lampimport turtle
import random

# 设置窗口
win = turtle.Screen()
win.title("Snake Game")  # 设置窗口标题为"Snake Game"
win.bgcolor("white")  # 设置窗口背景颜色为白色
win.setup(width=560, height=620)  # 设置窗口大小为560x620
win.tracer(0)  # 关闭窗口的自动更新，手动更新以提高性能

snake=[]  # 创建一个空列表来存储蛇的身体部分

paused = False  # 初始游戏状态为非暂停
game_over = False  # 初始游戏状态为非结束

# 绘制大边框
border_pen = turtle.Turtle()
border_pen.speed(0)
border_pen.hideturtle()
border_pen.color("black")
border_pen.pensize(3)
    
border_pen.penup()  # 画运动区
border_pen.goto(-250, -280)  # 左下角起点
border_pen.pendown()
border_pen.pensize(3)
    
for _ in range(4):   
    border_pen.forward(500)
    border_pen.left(90)

# 画状态区
border_pen.penup()  
border_pen.goto(-250, 220)  # 左上角起点
border_pen.pendown()
border_pen.left(90)
border_pen.forward(60)
border_pen.right(90)
border_pen.forward(500)
border_pen.right(90)
border_pen.forward(60)

# 创建运动方向变量和运动方向文本对象
direction = "stop"  # 初始方向为停止
status_bar = turtle.Turtle()
status_bar.hideturtle()
status_bar.penup()
status_bar.color("black")
status_bar.goto(-170, 240)
status_bar.write("Motion: ", align="center", font=("Arial", 18, "normal"))

# 创建计时器变量和计时器文本对象
game_time = 0
time_text = turtle.Turtle()
time_text.hideturtle()
time_text.penup()
time_text.color("black")
time_text.goto(0, 240)
time_text.write("Time: 0s", align="center", font=("Arial", 18, "normal"))

# 创建重叠次数文本对象
game_overlap_times=0
overlap_times = turtle.Turtle()
overlap_times.hideturtle()
overlap_times.penup()
overlap_times.color("black")
overlap_times.goto(170, 240)
overlap_times.write("Contact: 0 ", align="center", font=("Arial", 18, "normal"))

# 更新重叠次数的函数
def update_overlap_times():
    global game_overlap_times
    if not game_over and not paused:
        for i in range(1,len(snake)):
            for j in range(4):
                if i < len(snake) and j < len(monsters):
                    if snake[i].distance(monsters[j]) < 20:  # 检查每一个身体部分和每一个怪物的坐标关系
                        game_overlap_times+=1

    # 更新状态栏显示的重叠次数
    overlap_times.clear()
    overlap_times.write("Contact: " + str(game_overlap_times) , align="center", font=("Arial", 12, "normal"))

    win.ontimer(update_overlap_times, random_time)  # 每150-200毫秒（和怪物更新时间一致）调用update_overlap_times函数一次

# 更新时间的函数
def update_time():
    global game_time
    if not game_over:
        game_time += 1  # 游戏时间加1秒

    # 更新状态栏显示的游戏时间
    time_text.clear()
    time_text.write("Time: " + str(game_time) + "s", align="center", font=("Arial", 12, "normal"))

    # 每秒更新一次时间
    win.ontimer(update_time, 1000)  # 每1000毫秒（1秒）调用update_time函数一次
    
# 在游戏开始时启动计时器
update_time()

# 创建尾巴
def create_tail():
    tail = turtle.Turtle()  # 创建蛇的尾巴部分
    tail.speed(0)  # 设置尾巴移动速度为最快
    tail.shape("square")  # 设置尾巴形状为正方形
    tail.color("blue", "black")  # 设置尾巴颜色为蓝色边框，黑色填充
    tail.penup()  # 抬起画笔，不绘制轨迹
    if len(snake) > 0:
        tail.goto(snake[-1].xcor(), snake[-1].ycor())  # 将尾部定位在当前蛇尾部分
    else:
        tail.goto(0, -30)  # 将尾部定位在运动区中心
    snake.append(tail)  # 将尾巴添加到蛇的列表中

# 创建5个尾巴部分
for _ in range(5):
    tail = create_tail()

# 创建头部
head = turtle.Turtle()  # 创建蛇的头部
head.speed(0)  # 设置头部移动速度为最快
head.shape("square")  # 设置头部形状为正方形
head.color("red")  # 设置头部颜色为红色
head.penup()  # 抬起画笔，不绘制轨迹
head.goto(0, -30)  # 将头部定位在运动区中心
snake.insert(0, head)  # 将头部插入到蛇的列表的开头

# 随机生成食物的函数
def create_food(number):
    food = turtle.Turtle()
    food.speed(0)
    food.shape("square")
    food.color("black")
    food.penup()
    food.hideturtle()
    
    x = random.randint(0, 24)*20-240
    y = random.randint(0, 24)*20-46-240  #经调试这个值可以使蛇头和食物对齐

    # 计算文本的位置，使其位于色块的正中间
    food.goto(x,y)  
    food.write(number, align="center", font=("Arial", 18, "bold"))
    
    # 将文本移动到色块正中间
    text_x, text_y = food.position()
    text_offset = 16  # 文本的偏移量，可以根据需要调整
    food.goto(text_x, text_y + text_offset)
    
    return food

foods = []  # 生成食物列表

def create_monster():
    monster = turtle.Turtle()
    monster.speed(0)
    monster.shape("square")
    monster.color("purple")
    monster.penup()
    
    x = random.choice([random.randint(-240, 100),random.randint(100, 240)]) #将怪物放置在距离蛇头足够远的随机位置
    y = random.choice([random.randint(-270, -130),random.randint(70, 210)])

    monster.goto(x, y)

    return monster

monsters = []  # 生成怪物

#吃掉食物的函数
def update_food():
    global velocity_change
    
    for food in foods:
        if food.distance(snake[0]) < 20:  # 当蛇头接近食物时
            food.clear()  # 清除食物
            velocity_change=25
            food_number=int(food.my_value)
            for _ in range(food_number):
                tail = create_tail()
            foods.remove(food)  # 从食物列表中移除被吃掉的食物

def encounter_monster():
    global game_over
    
    for monster in monsters:
        if monster.distance(snake[0]) < 20:  # 当蛇头碰到怪物时
            game_over = True

            loser_message = turtle.Turtle()  # 显示失败消息
            loser_message.hideturtle()
            loser_message.penup()
            loser_message.color("black")
            loser_message.goto(0, -30)
            loser_message.write("Game Over !!", align="center", font=("Arial", 24, "bold"))
        
# 创建食物移动函数
def move_food():
    for food in foods:
        if random.random() < 0.75:  # 75%的概率移动食物
            direction = random.choice(["up", "down", "left", "right"])
            distance = 40

            x = food.xcor()
            y = food.ycor()-16  #这个值经是调试过的，使得蛇头和食物对齐
                
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
                food_number=int(food.my_value) #获取食物数值
                food.write(food_number, align="center", font=("Arial", 18, "bold"))

                # 将文本移动到色块正中间
                text_x, text_y = food.position()
                text_offset = 16  # 文本的偏移量，可以根据需要调整
                food.goto(text_x, text_y + text_offset)

# 设置随机移动计时器
def random_move_food():
    if not game_over:
        move_food()
        random_time = random.randint(5, 10)  # 随机生成移动时间间隔（秒）
        turtle.ontimer(random_move_food, random_time * 1000)  # 将函数设置为在随机时间后再次调用

# 在游戏开始时启动随机移动食物的计时器
random_move_food()


# 怪物移动函数
def move_monsters():
    for monster in monsters:
        if random.random() < 0.25:  # 25%的概率移动怪物
            x = monster.xcor()
            y = monster.ycor()

            # 计算怪物与蛇头的相对位置
            dx = snake[0].xcor() - x
            dy = snake[0].ycor() - y

            # 根据相对位置决定怪物的移动方向
            if abs(dx) > abs(dy):  # 水平方向距离较大
                if dx > 0:
                    x += 20  # 向右移动
                else:
                    x -= 20  # 向左移动
            else:  # 垂直方向距离较大
                if dy > 0:
                    y += 20  # 向上移动
                else:
                    y -= 20  # 向下移动

            monster.goto(x, y)  # 更新怪物的位置

# 控制蛇的移动
def move():
    global direction,status_bar
    if not paused and not game_over:
        x = snake[0].xcor()  # 获取蛇头的x坐标
        y = snake[0].ycor()  # 获取蛇头的y坐标

        if (x < -230 and direction=="left") or (x > 230 and direction=="right") or (y < -260 and direction=="down") or (y > 200 and direction=="up"):
            direction = "stop"  #越界方向与运动方向一致则停止

        # 移动尾巴
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

        snake[0].goto(x, y)  # 更新蛇头的位置

        check_game() # 检查是否游戏结束

        update_food()  # 更新食物位置

        # 更新状态栏显示的上次按下的移动键
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

# 检查是否游戏结束的函数
def check_game():
    global game_over
    
    encounter_monster() # 看看是否撞击怪物
    
    for i in range(1,len(snake)):
        if snake[i-1].distance(snake[i]) != 20:  # 检查每一个身体部分和前一个身体部分的坐标关系
            return
        
    if len(foods)!=0 or len(snake)!=21 :  # 如果食物被吃完且长度到达最长
        return
            
    game_over=True  # 如果都满足条件，游戏结束

    winner_message = turtle.Turtle()  # 显示胜利消息
    winner_message.hideturtle()
    winner_message.penup()
    winner_message.color("black")
    winner_message.goto(0, -30)
    winner_message.write("Winner !!", align="center", font=("Arial", 24, "bold"))

# 显示开始消息
start_message = turtle.Turtle()
start_message.hideturtle()
start_message.penup()
start_message.color("black")
start_message.goto(0, 0)
start_message.write("Click anywhere to start, have fun!!!!", align="center", font=("Arial", 16, "normal"))

# 点击开始游戏
def start_game(x, y):
    global food, start_message
    win.onclick(None)  # 取消点击事件
    start_message.clear()  # 清除开始消息
    
    for i in range(1, 6):   # 生成食物
        food = create_food(i)
        food.my_value = i
        foods.append(food)

    for j in range(4):   # 生成怪物
        monster = create_monster()
        monsters.append(monster)

    win.listen()  # 开启键盘监听

win.onclick(start_game)

# 设置键盘绑定
win.onkey(go_up, "Up")  # 绑定向上键与go_up函数
win.onkey(go_down, "Down")  # 绑定向下键与go_down函数
win.onkey(go_left, "Left")  # 绑定向左键与go_left函数
win.onkey(go_right, "Right")  # 绑定向右键与go_right函数
win.onkey(toggle_pause, "space")  # 绑定空格键与toggle_pause函数

velocity_change=0 # 该变量用于记录吃掉食物时的速度变化
    
# 创建定时器手动刷新蛇的运动
def update_snake_movement():
    global velocity_change
    move()  # 更新蛇的位置
    win.update()  # 更新窗口
    if not game_over:
        if snake[-1].distance(snake[-2]) != 0:  # 完全伸展速度变为正常
            velocity_change=0
        turtle.ontimer(update_snake_movement, 175+velocity_change)  # 每175毫秒调用一次update_snake_movement函数

# 创建定时器手动刷新怪物的运动

random_time=0 # 怪物运动随机时间(也是刷新重叠次数的时间)

def update_monster_movement():
    global random_time
    move_monsters()  # 更新怪物的位置
    win.update()  # 更新窗口
    if not game_over :
        random_time=random.randint(150,200)
        turtle.ontimer(update_monster_movement, random_time)  # 每150-200毫秒调用一次update_monster_movement函数

# 在游戏开始时启动蛇和怪物的定时器
update_snake_movement()
update_monster_movement()

# 在游戏开始时启动重叠次数计数器
update_overlap_times()

turtle.done()  # 让窗口保持运行状态
