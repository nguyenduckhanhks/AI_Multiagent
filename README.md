# AI_Multiagent
# Question 1 (4 points): Reflex AgentQuestion 1:
+ Hoàn thành evaluationFunction: 
# Ý tưởng: Dựa trên khoảng cách nhỏ nhất giữa pacman và food với pacman và ghost:
+ Trong trường hợp ma quá gần pacman (1 ô) thì return âm vô cùng (nhỏ nhất)
+ Các trường hợp còn lại return về điểm đang có + 1 / (khoảng cách ngắn nhất đến food)
+ Do food càng gần thì càng đánh giá cao => evalution cao => cần lây nghịch đảo
# Giải thuật
```
minPosFood = float('inf')
for food in newFood.asList():
  minPosFood = min(minPosFood,manhattanDistance(newPos,food))

minPosGhost = float('inf')
for ghost in newGhostStates:
  minPosGhost = min(minPosGhost, manhattanDistance(newPos, ghost.getPosition()))
cost = minPosFood
if(minPosGhost <= 1):
  cost = -float('inf')
return successorGameState.getScore() + 1.0/cost
```
# Question 2 (5 points): Minimax
# ý tưởng: cài hàm minimax
+ Hàm trả về evalution của node và hướng đi
+ Lượt của pacman đóng vai trò là max
+ Lượt của ma đóng vai trò là min
+ mỗi lần duyệt một đối tượng sẽ duyệt qua tất cả các action mà nó có thể đi; sau đó tìm ra min(max) của chúng và return lại tại điểm đó
+ Sau mỗi lần duyệt qua tất cả ma và pacman thì độ sâu của thuật toán tăng lên 1
+ Khi không có action có thể đi hoặc đạt độ sâu tối đa thì đó là node lá
+ node lá return về evalution tại state đó và hướng đi là 0
+ giải thuật theo đề quy

+ Giải thuật
```
def minimax(self, gameState, agent, depth):
  if not gameState.getLegalActions(agent):
      return self.evaluationFunction(gameState),0

  if depth == self.depth:
      return self.evaluationFunction(gameState),0

  if agent == gameState.getNumAgents() - 1:
      newAgent = 0
  else :
      newAgent = agent + 1

  if agent == agent == gameState.getNumAgents() - 1:
      depth = depth + 1
  if agent == self.index:
      best = []
      best.append(-float('inf'))
      best.append(0)
      for action in gameState.getLegalActions(agent):
          new = self.minimax(gameState.generateSuccessor(agent,action),newAgent,depth)
          if new[0] > best[0] :
              best[0] = new[0]
              best[1] = action
      return best
  else :
      best = []
      best.append(float('inf'))
      best.append(0)
      for action in gameState.getLegalActions(agent):
          new = self.minimax(gameState.generateSuccessor(agent,action),newAgent,depth)
          if new[0] < best[0] :
              best[0] = new[0]
              best[1] = action
      return best
```
# Question 3 (5 points): Alpha-Beta Pruning
# Ý tưởng: về cơ bản giống minimax; nhưng có thêm hai tham số alpha và beta
+ alpha là lựa chọn tốt nhất của max cho root
+ beta là lựa chọn tốt nhất của min cho root
+ khi đó ở trường hợp của pacman: + khi tìm kiếm max; nếu giá trị max tạm thời lớn hơn beta thì return luôn giá trị đó đồng thời bỏ qua các trường hợp còn lại.
                                  + ở mỗi lần duyệt sẽ tìm ra giá trị max và gán cho a.
+ tương tự với trường hợp của các con ma.
# giải thuật;
```
def alphaBeta(self, gameState, agent, depth, a, b):
  if not gameState.getLegalActions(agent):
      return self.evaluationFunction(gameState),0

  if depth == self.depth:
      return self.evaluationFunction(gameState),0

  if agent == gameState.getNumAgents() - 1:
      newAgent = 0
  else :
      newAgent = agent + 1

  if agent == agent == gameState.getNumAgents() - 1:
      depth = depth + 1
  if agent == self.index:
      best = []
      best.append(-float('inf'))
      best.append(0)
      for action in gameState.getLegalActions(agent):
          new = self.alphaBeta(gameState.generateSuccessor(agent,action),newAgent,depth, a, b)
          if new[0] > best[0] :
              best[0] = new[0]
              best[1] = action
          if best[0] > b:
              return best
          a = max(a, best[0])
      return best
  else :
      best = []
      best.append(float('inf'))
      best.append(0)
      for action in gameState.getLegalActions(agent):
          new = self.alphaBeta(gameState.generateSuccessor(agent,action),newAgent,depth, a, b)
          if new[0] < best[0] :
              best[0] = new[0]
              best[1] = action
          if best[0] < a:
              return best
          b = min(best[0], b)
      return best
 ```
# Question 4 (5 points): Expectimax

# ý tưởng: tương tự minimax; chỉ khác ở trường hợp của các con ma:
+ Thay vì tìm giá trị min của các node ở dưới thì ở đây ta sẽ lấy trung bình evalution của chúng. ta chỉ lấy một action để xét (action cuối cùng)
# giải thuật :
```
def expectimax(self, gameState, agent, depth):
  if not gameState.getLegalActions(agent):
      return self.evaluationFunction(gameState),0

  if depth == self.depth:
      return self.evaluationFunction(gameState),0

  if agent == gameState.getNumAgents() - 1:
      newAgent = 0
  else :
      newAgent = agent + 1

  if agent == agent == gameState.getNumAgents() - 1:
      depth = depth + 1
  if agent == self.index:
      best = []
      best.append(-float('inf'))
      best.append(0)
      for action in gameState.getLegalActions(agent):
          new = self.expectimax(gameState.generateSuccessor(agent,action),newAgent,depth)
          if new[0] > best[0] :
              best[0] = new[0]
              best[1] = action
      return best
  else :
      best = []
      best.append(0.0)
      best.append(0)
      for action in gameState.getLegalActions(agent):
          new = self.expectimax(gameState.generateSuccessor(agent,action),newAgent,depth)
          
          best[0] = best[0] + (1.0/len(gameState.getLegalActions(agent))) * new[0]
          best[1] = action
      return best
 ```
# Question 5 (6 points): Evaluation Function
# ý tưởng: khá giống bài 1.
+ Tuy nhiên nhận thấy khi ma cách xa pacman; nó hay dừng lại không làm gì
+ do đó; trong trường hợp con ma không quá gần pacman; thì evalution của ta sẽ trừ đi khoảng cách từ ma đến pacman nhỏ nhất
+ từ đó pacman sẽ không dính phải trường hợp đứng yên
# giải thuật:
```
newPos = currentGameState.getPacmanPosition()
newFood = currentGameState.getFood()
newGhostStates = currentGameState.getGhostStates()
newScaredTimes = [ghostState.scaredTimer for ghostState in newGhostStates]

minPosFood = float('inf')
for food in newFood.asList():
  minPosFood = min(minPosFood,manhattanDistance(newPos,food))

minPosGhost = float('inf')
for ghost in newGhostStates:
  minPosGhost = min(minPosGhost, manhattanDistance(newPos, ghost.getPosition()))
cost = minPosFood
if(minPosGhost <= 1):
  cost = -float('inf')
return currentGameState.getScore() + 1.0/cost - minPosGhost
