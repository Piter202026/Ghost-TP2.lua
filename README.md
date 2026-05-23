# Ghost-TP2.lua

-- LocalScript (deve estar em StarterPlayerScripts)
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- 1. CRIAR GUI PRINCIPAL ------------------------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GiantSimExploitUI"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = player:WaitForChild("PlayerGui")

-- 2. FRAME PRINCIPAL DA GUI (arrastável & redimensionável) --------------------
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 280, 0, 350)
mainFrame.Position = UDim2.new(0.5, -140, 0.5, -175)
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = screenGui

-- Borda arredondada
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 6)
UICorner.Parent = mainFrame

-- Barra de título (para arrastar)
local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleText = Instance.new("TextLabel")
titleText.Name = "TitleText"
titleText.Size = UDim2.new(1, -50, 1, 0)
titleText.Position = UDim2.new(0, 5, 0, 0)
titleText.Text = "Giant Sim Exploit"
titleText.TextColor3 = Color3.fromRGB(255, 255, 255)
titleText.TextSize = 16
titleText.Font = Enum.Font.GothamBold
titleText.BackgroundTransparency = 1
titleText.Parent = titleBar

-- Botão minimizar/maximizar (esquerda do título)
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 25, 0, 25)
toggleButton.Position = UDim2.new(1, -30, 0.5, -12.5)
toggleButton.AnchorPoint = Vector2.new(1, 0.5)
toggleButton.Text = "-"
toggleButton.TextSize = 18
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.BackgroundColor3 = Color3.fromRGB(80, 120, 255)
toggleButton.BorderSizePixel = 0
toggleButton.Parent = titleBar

-- Botão fechar (direita do título)
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -55, 0.5, -12.5)
closeButton.AnchorPoint = Vector2.new(1, 0.5)
closeButton.Text = "X"
closeButton.TextSize = 16
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.GothamBold
closeButton.BackgroundColor3 = Color3.fromRGB(220, 80, 80)
closeButton.BorderSizePixel = 0
closeButton.Parent = titleBar

-- Conteúdo principal (toggleSection)
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1, 0, 1, -30)
contentFrame.Position = UDim2.new(0, 0, 0, 30)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

-- TOGGLE SECTION ------------------------------------------------------------
local function createToggle(name, positionY, defaultState, color)
local toggleFrame = Instance.new("Frame")
toggleFrame.Name = name.. "ToggleFrame"
toggleFrame.Size = UDim2.new(0.95, 0, 0, 50)
toggleFrame.Position = UDim2.new(0.5, -130, 0, positionY)
toggleFrame.AnchorPoint = Vector2.new(0.5, 0)
toggleFrame.BackgroundTransparency = 1
toggleFrame.Parent = contentFrame

local toggleLabel = Instance.new("TextLabel")
toggleLabel.Name = name.. "Label"
toggleLabel.Size = UDim2.new(0.8, 0, 1, 0)
toggleLabel.Position = UDim2.new(0, 0, 0, 0)
toggleLabel.Text = name
toggleLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
toggleLabel.TextSize = 14
toggleLabel.Font = Enum.Font.Gotham
toggleLabel.BackgroundTransparency = 1
toggleLabel.TextXAlignment = Enum.TextXAlignment.Left
toggleLabel.Parent = toggleFrame

local toggle = Instance.new("TextButton")
toggle.Name = name.. "Toggle"
toggle.Size = UDim2.new(0, 40, 0, 20)
toggle.Position = UDim2.new(1, -45, 0.5, -10)
toggle.AnchorPoint = Vector2.new(1, 0.5)
toggle.Text = ""
toggle.BackgroundColor3 = defaultState and color or Color3.fromRGB(200, 200, 200)
toggle.BorderSizePixel = 0
toggle.ClipsDescendants = true
toggle.Parent = toggleFrame

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(1, 0)
toggleCorner.Parent = toggle

local toggleDot = Instance.new("Frame")
toggleDot.Name = "Dot"
toggleDot.Size = UDim2.new(0, 16, 0, 16)
toggleDot.Position = UDim2.new(0, 0, 0, 2)
toggleDot.AnchorPoint = Vector2.new(0, 0)
toggleDot.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
toggleCorner.CornerRadius = UDim.new(1, 0)
toggleDot.Parent = toggle

toggle.Frame = toggleDot

return {
frame = toggleFrame,
button = toggle,
state = defaultState
}
end

-- Criar 4 toggles
local toggles = {
autoWeightPro = createToggle("AUTO WEIGHT 💪 (PRO)", 20, false, Color3.fromRGB(100, 200, 100)),
autoWeightBeginner = createToggle("PESO AUTOMÁTICO 💪 (INICIANTE)", 80, false, Color3.fromRGB(150, 220, 150)),
autoBuy = createToggle("AUTO BUY 🛒", 140, false, Color3.fromRGB(200, 200, 100)),
infDaily = createToggle("✨ INF DAILY ✨", 200, false, Color3.fromRGB(220, 100, 220))
}

-- 3. FUNÇÕES DE TOGGLE E ARRASTAR -------------------------------------------
-- Função de toggle (clique no botão redondo)
local function toggleState(toggleData)
toggleData.state = not toggleData.state
toggleData.button.BackgroundColor3 = toggleData.state and toggles.autoWeightPro.button.BackgroundColor3 or Color3.fromRGB(200, 200, 200)
toggleData.button.Frame.Position = toggleData.state and UDim2.new(1, -22, 0.5, -8) or UDim2.new(0, 0, 0.5, -8)
print(toggleData.frame.Name.. " toggled to: ".. tostring(toggleData.state))
end

-- Bind clicks nos 4 toggles
for _, toggle in pairs(toggles) do
toggle.button.MouseButton1Click:Connect(function()
toggleState(toggle)
end)
end

-- Função de arrastar pela barra de título
local dragging = false
local dragStartPos = Vector2.new()
local guiStartPos = Vector2.new()

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

local function stopDrag()
dragging = false
end

titleBar.InputBegan:Connect(startDrag)
titleBar.InputChanged:Connect(function(input)
if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
drag(input)
end
end)
UserInputService.InputEnded:Connect(function(input)
if input.UserInputType == Enum.UserInputType.MouseButton1 then
stopDrag()
end
end)

-- Minimizar/maximizar
local toggledClosed = false
toggleButton.MouseButton1Click:Connect(function()
toggledClosed = not toggledClosed
toggleButton.Text = toggledClosed and "+" or "-"

if toggledClosed then
contentFrame.Visible = false
mainFrame.Size = UDim2.new(0, 280, 0, 30)
else
contentFrame.Visible = true
mainFrame.Size = UDim2.new(0, 280, 0, 350)
end
end)

-- Fechar GUI
closeButton.MouseButton1Click:Connect(function()
screenGui:Destroy()
print("GUI fechada manualmente.")
end)

-- 4. FUNÇÕES DE AUTOMAÇÃO (código base com prints) ----------------------------
local autoWeightProActive = false
local autoWeightBeginnerActive = false
local autoBuyActive = false
local infDailyActive = false

local function updateTogglesState()
autoWeightProActive = toggles.autoWeightPro.state
autoWeightBeginnerActive = toggles.autoWeightBeginner.state
autoBuyActive = toggles.autoBuy.state
infDailyActive = toggles.infDaily.state
end

RunService.Heartbeat:Connect(function()
updateTogglesState()

if autoWeightProActive then
-- AUTO WEIGHT PRO
local giant = character:FindFirstChildOfClass("Model") or character
if giant and humanoid then
local rootPart = humanoid.RootPart
if rootPart then
local weights = workspace:FindPartsInRadius(rootPart.Position, 15, {IgnoreWater = false})
for _, part in pairs(weights) do
if part.Name == "Weight" and part:FindFirstChild("Handle") then
local bodyVelocity = Instance.new("BodyVelocity")
bodyVelocity.Velocity = Vector3.new(0, 5000, 0)
bodyVelocity.MaxForce = Vector3.new(0, math.huge, 0)
bodyVelocity.P = math.huge
bodyVelocity.Parent = part
task.delay(0.5, function()
bodyVelocity:Destroy()
end)
print("AUTO WEIGHT PRO: Peso detectado, aplicando força...")
break
end
end
end
end
end

if autoWeightBeginnerActive then
-- PESO AUTOMÁTICO INICIANTE
if humanoid then
if humanoid:GetState() ~= Enum.HumanoidStateType.Swimming then
humanoid:ChangeState(Enum.HumanoidStateType.Swimming)
end
task.wait(0.1)
print("PESO AUTOMÁTICO INICIANTE: Mantendo estado ajoelhado...")
end
end

if autoBuyActive then
-- AUTO BUY (itens pré-definidos)
local replicatedStorage = game:GetService("ReplicatedStorage")
local remotes = replicatedStorage:FindFirstChild("Remotes")
if remotes then
local buyItem = remotes:FindFirstChild("BuyItem")
if buyItem and replicatedStorage:FindFirstChild("Stats") then
local money = replicatedStorage.Stats:FindFirstChild("Money")
if money and money.Value >= 5000 then
local items = {
["Strength Boots"] = 12345678,
["Giant Suit"] = 87654321,
["Weight Vest"] = 11223344
}
for name, id in pairs(items) do
pcall(function()
buyItem:FireServer(id)
print("AUTO BUY: Comprado ".. name)
end)
task.wait(3)
end
end
end
end
end

if infDailyActive then
-- INF DAILY (reiniciar diário)
if humanoid and humanoid.RootPart then
local topPlatform = workspace:FindFirstChild("TopPlatform") or workspace:FindFirstChild("Map") and workspace.Map:FindFirstChild("TopPlatform")
if topPlatform and humanoid.RootPart.Position.Y > topPlatform.Position.Y + 50 then
local replicatedStorage = game:GetService("ReplicatedStorage")
local remotes = replicatedStorage:FindFirstChild("Remotes")
if remotes then
local claimDaily = remotes:FindFirstChild("ClaimDaily")
if claimDaily then
pcall(function()
claimDaily:FireServer()
print("INF DAILY: Diário resetado...")
end)
end
end
task.wait(2)
end
end
end
end)
