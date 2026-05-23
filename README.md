-- Ghost TP1 (script único pra rodar no Executor)
-- IDE: script de uma vez só, sem pastas ou módulos externos
-- 0. PREPARAR SERVIÇOS -------------------------------------------------------

local Services = {
Players = game:GetService("Players"),
UserInputService = game:GetService("UserInputService"),
TweenService = game:GetService("TweenService"),
RunService = game:GetService("RunService"),
ReplicatedStorage = game:GetService("ReplicatedStorage"),
Workspace = game:GetService("Workspace")
}

local player = Services.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- 1. GUI NO EXECUTOR (ScreenGui + Frame) -------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ExecutorGiantSimUI"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Main Frame (arrastável)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 280, 0, 350)
mainFrame.Position = UDim2.new(0.5, -140, 0.5, -175)
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = screenGui

-- Bordas arredondadas
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 6)
UICorner.Parent = mainFrame

-- Barra de título
local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleText = Instance.new("TextLabel")
titleText.Name = "TitleText"
titleText.Size = UDim2.new(1, -90, 1, 0)
titleText.Position = UDim2.new(0, 5, 0, 0)
titleText.Text = "Giant Sim Exploit (Executor)"
titleText.TextColor3 = Color3.fromRGB(255, 255, 255)
titleText.TextSize = 16
titleText.Font = Enum.Font.GothamBold
titleText.BackgroundTransparency = 1
titleText.Parent = titleBar

-- Botão minimizar
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 25, 0, 25)
toggleButton.Position = UDim2.new(1, -60, 0.5, -12.5)
toggleButton.AnchorPoint = Vector2.new(1, 0.5)
toggleButton.Text = "-"
toggleButton.TextSize = 18
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.BackgroundColor3 = Color3.fromRGB(80, 120, 255)
toggleButton.BorderSizePixel = 0
toggleButton.Parent = titleBar

-- Botão fechar
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -30, 0.5, -12.5)
closeButton.AnchorPoint = Vector2.new(1, 0.5)
closeButton.Text = "X"
closeButton.TextSize = 16
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.GothamBold
closeButton.BackgroundColor3 = Color3.fromRGB(220, 80, 80)
closeButton.BorderSizePixel = 0
closeButton.Parent = titleBar

-- Content Frame (toggles)
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1, 0, 1, -30)
contentFrame.Position = UDim2.new(0, 0, 0, 30)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

-- 2. CRIAR 4 TOGGLES ----------------------------------------------------------
local function createToggle(name, posY, default)
local tFrame = Instance.new("Frame")
tFrame.Name = name.. "ToggleFrame"
tFrame.Size = UDim2.new(0.95, 0, 0, 50)
tFrame.Position = UDim2.new(0.5, -130, 0, posY)
tFrame.AnchorPoint = Vector2.new(0.5, 0)
tFrame.BackgroundTransparency = 1
tFrame.Parent = contentFrame

local tLabel = Instance.new("TextLabel")
tLabel.Name = name.. "Label"
tLabel.Size = UDim2.new(0.75, 0, 1, 0)
tLabel.Position = UDim2.new(0, 0, 0, 0)
tLabel.Text = name
tLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
tLabel.TextSize = 14
tLabel.Font = Enum.Font.Gotham
tLabel.BackgroundTransparency = 1
tLabel.TextXAlignment = Enum.TextXAlignment.Left
tLabel.Parent = tFrame

local tButton = Instance.new("TextButton")
tButton.Name = name.. "Toggle"
tButton.Size = UDim2.new(0, 40, 0, 20)
tButton.Position = UDim2.new(1, -45, 0.5, -10)
tButton.AnchorPoint = Vector2.new(1, 0.5)
tButton.Text = ""
tButton.BackgroundColor3 = default and Color3.fromRGB(100, 200, 100) or Color3.fromRGB(200, 200, 200)
tButton.BorderSizePixel = 0
tButton.ClipsDescendants = true
tButton.Parent = tFrame

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(1, 0)
UICorner.Parent = tButton

local tDot = Instance.new("Frame")
tDot.Name = "Dot"
tDot.Size = UDim2.new(0, 16, 0, 16)
tDot.Position = default and UDim2.new(0.5, -12, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
tDot.AnchorPoint = Vector2.new(0, 0.5)
tDot.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
local uic = Instance.new("UICorner")
uic.CornerRadius = UDim.new(1, 0)
uic.Parent = tDot
tDot.Parent = tButton

return {button = tButton, state = default}
end

local toggles = {
autoWeightPro = createToggle("AUTO WEIGHT 💪 (PRO)", 20, false),
autoWeightBeginner = createToggle("PESO AUTOMÁTICO 💪 (INICIANTE)", 80, false),
autoBuy = createToggle("AUTO BUY 🛒", 140, false),
infDaily = createToggle("✨ INF DAILY ✨", 200, false)
}

-- 3. FUNÇÕES DE GUI (fechar/minimizar/arrastar) --------------------------------
local toggledClosed = false
toggleButton.MouseButton1Click:Connect(function()
toggledClosed = not toggledClosed
toggleButton.Text = toggledClosed and "+" or "-"
contentFrame.Visible = not toggledClosed
mainFrame.Size = toggledClosed and UDim2.new(0, 280, 0, 30) or UDim2.new(0, 280, 0, 350)
end)

closeButton.MouseButton1Click:Connect(function()
screenGui:Destroy()
print("GUI encerrada pelo Executor.")
end)

-- Arrastar
local dragging, dragStartPos, guiStartPos = false, nil, nil
local function startDrag(input)
if input.UserInputType == Enum.UserInputType.MouseButton1 and titleBar.AbsolutePosition.X <= input.Position.X and input.Position.X <= titleBar.AbsolutePosition.X + titleBar.AbsoluteSize.X and
titleBar.AbsolutePosition.Y <= input.Position.Y and input.Position.Y <= titleBar.AbsolutePosition.Y + titleBar.AbsoluteSize.Y then
dragging = true
dragStartPos = Vector2.new(input.Position.X, input.Position.Y)
guiStartPos = Vector2.new(mainFrame.Position.X.Offset, mainFrame.Position.Y.Offset)
end
end
local function drag(input)
if dragging then
local offset = Vector2.new(input.Position.X, input.Position.Y) - dragStartPos
mainFrame.Position = UDim2.new(0, guiStartPos.X + offset.X, 0, guiStartPos.Y + offset.Y)
end
end
titleBar.InputBegan:Connect(startDrag)
Services.UserInputService.InputChanged:Connect(function(input)
if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
drag(input)
end
end)
Services.UserInputService.InputEnded:Connect(function(input)
if input.UserInputType == Enum.UserInputType.MouseButton1 then
dragging = false
end
end)

-- 4. TOGGLE STATE -------------------------------------------------------------
local toggleStates = {
autoWeightPro = false,
autoWeightBeginner = false,
autoBuy = false,
infDaily = false
}

local function setToggleState(name)
toggleStates[name] = not toggleStates[name]
local t = toggles[name]
t.state = toggleStates[name]
t.button.BackgroundColor3 = toggleStates[name] and (name == "autoWeightPro" and Color3.fromRGB(100, 200, 100) or
name == "autoWeightBeginner" and Color3.fromRGB(150, 220, 150) or
name == "autoBuy" and Color3.fromRGB(200, 200, 100) or
Color3.fromRGB(220, 100, 220)) or Color3.fromRGB(200, 200, 200)
print(name.. " status: ".. tostring(toggleStates[name]))
end

for name, t in pairs(toggles) do
t.button.MouseButton1Click:Connect(function()
setToggleState(name)
end)
end

-- 5. FUNÇÕES DE AUTOMAÇÃO (sem RemoteDetection, usa ReplicatedStorage direto) ---
local function autoWeightProFunc()
if not toggleStates.autoWeightPro then return end
if not humanoid or not humanoid.RootPart then return end
local weights = Services.Workspace:FindPartsInRadius(humanoid.RootPart.Position, 15, {IgnoreWater = false})
for _, w in pairs(weights) do
if w.Name == "Weight" and w:FindFirstChild("Handle") then
w:ApplyImpulse(Vector3.new(0, 5000, 0)) -- Impulso vertical fixo
print("Executor: AUTO WEIGHT PRO aplicou impulso no peso.")
break
end
end
end

local function autoWeightBeginnerFunc()
if not toggleStates.autoWeightBeginner then return end
if humanoid then
humanoid:ChangeState(Enum.HumanoidStateType.Swimming) -- estado ajoelhado
print("Executor: PESO AUTOMÁTICO INICIANTE mantido.")
end
end

local function autoBuyFunc()
if not toggleStates.autoBuy then return end
local remotes = Services.ReplicatedStorage:FindFirstChild("Remotes")
if not remotes then return end
local buyItem = remotes:FindFirstChild("BuyItem")
if not buyItem then return end
local stats = Services.ReplicatedStorage:FindFirstChild("Stats")
if not stats then return end
local money = stats:FindFirstChild("Money")
if not money or money.Value < 5000 then return end

local items = {
["Strength Boots"] = 12345678,
["Giant Suit"] = 87654321,
["Weight Vest"] = 11223344
}
for name, id in pairs(items) do
pcall(function()
buyItem:FireServer(id)
print("Executor: AUTO BUY comprou ".. name)
end)
task.wait(1.5)
end
end

local function infDailyFunc()
if not toggleStates.infDaily then return end
local top = Services.Workspace:FindFirstChild("TopPlatform") or (Services.Workspace:FindFirstChild("Map") and Services.Workspace.Map:FindFirstChild("TopPlatform"))
if top and humanoid and humanoid.RootPart and humanoid.RootPart.Position.Y > top.Position.Y + 50 then
local claimDaily = remotes and remotes:FindFirstChild("ClaimDaily")
if claimDaily then
pcall(function()
claimDaily:FireServer()
print("Executor: INF DAILY reiniciou diário.")
end)
end
end
end

-- 6. LOOP DE EXECUÇÃO (Heartbeat no Executor) --------------------------------
Services.RunService.Heartbeat:Connect(function()
autoWeightProFunc()
autoWeightBeginnerFunc()
autoBuyFunc()
infDailyFunc()
end)

print("Script ativado pelo Executor. GUI criada.")
