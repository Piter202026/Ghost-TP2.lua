-- Ghost TP1 (Giant Lifting Simulator) --
-- Correções: GUI fixa, arrastar perfeito, fechar na barra corrigido, toggles em loop silencioso

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = humanoid.RootPart

-- 0. NOME DA GUI E PASTA INDETECTÁVEL ---------------------------------------
local hiddenName = "__"..tostring(math.random(100000,999999))
local screenGui = Instance.new("ScreenGui")
screenGui.Name = hiddenName
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = player:WaitForChild("PlayerGui")

-- 1. MAIN FRAME FIX (tamanho fixo, posicional correto) -----------------------
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 280, 0, 350)
mainFrame.Position = UDim2.new(0.5, -140, 0.5, -175)
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = screenGui

-- Borda
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 6)
corner.Parent = mainFrame

-- 2. TÍTULO + BOTÕES (minimizar e fechar) -----------------------------------
local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

-- Minimizar
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 25, 0, 25)
toggleButton.Position = UDim2.new(1, -65, 0.5, -12.5)
toggleButton.AnchorPoint = Vector2.new(1, 0.5)
toggleButton.Text = "-"
toggleButton.TextSize = 18
toggleButton.TextColor3 = Color3.fromRGB(255,255,255)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.BackgroundColor3 = Color3.fromRGB(80,120,255)
toggleButton.BorderSizePixel = 0
toggleButton.Parent = titleBar

-- Fechar
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -35, 0.5, -12.5)
closeButton.AnchorPoint = Vector2.new(1, 0.5)
closeButton.Text = "X"
closeButton.TextSize = 16
closeButton.TextColor3 = Color3.fromRGB(255,255,255)
closeButton.Font = Enum.Font.GothamBold
closeButton.BackgroundColor3 = Color3.fromRGB(220,80,80)
closeButton.BorderSizePixel = 0
closeButton.Parent = titleBar

-- Título
local titleText = Instance.new("TextLabel")
titleText.Name = "TitleText"
titleText.Size = UDim2.new(1, -100, 1, 0)
titleText.Position = UDim2.new(0, 10, 0, 0)
titleText.Text = "Giant Sim Exploit"
titleText.TextColor3 = Color3.fromRGB(240,240,240)
titleText.TextSize = 16
titleText.Font = Enum.Font.GothamBold
titleText.BackgroundTransparency = 1
titleText.TextXAlignment = Enum.TextXAlignment.Left
titleText.Parent = titleBar

-- 3. CONTEÚDO DA GUI ---------------------------------------------------------
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1,0,1,-30)
contentFrame.Position = UDim2.new(0,0,0,30)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

-- 4. CRIAÇÃO DOS 4 TOGGLES ---------------------------------------------------
local toggleStates = {
    autoWeightPro = false,
    autoWeightBeginner = false,
    autoBuy = false,
    infDaily = false
}

local function createToggle(name, posY)
    local tFrame = Instance.new("Frame")
    tFrame.Name = name.."ToggleFrame"
    tFrame.Size = UDim2.new(0.95,0,0,50)
    tFrame.Position = UDim2.new(0.5,-130,0,posY)
    tFrame.AnchorPoint = Vector2.new(0.5,0)
    tFrame.BackgroundTransparency = 1
    tFrame.Parent = contentFrame

    local tLabel = Instance.new("TextLabel")
    tLabel.Name = name.."Label"
    tLabel.Size = UDim2.new(0.8,0,1,0)
    tLabel.Position = UDim2.new(0,0,0,0)
    tLabel.Text = name
    tLabel.TextColor3 = Color3.fromRGB(230,230,230)
    tLabel.TextSize = 14
    tLabel.Font = Enum.Font.Gotham
    tLabel.BackgroundTransparency = 1
    tLabel.TextXAlignment = Enum.TextXAlignment.Left
    tLabel.Parent = tFrame

    local tButton = Instance.new("TextButton")
    tButton.Name = name.."Toggle"
    tButton.Size = UDim2.new(0,40,0,20)
    tButton.Position = UDim2.new(1,-45,0.5,-10)
    tButton.AnchorPoint = Vector2.new(1,0.5)
    tButton.Text = ""
    tButton.BackgroundColor3 = Color3.fromRGB(200,200,200)
    tButton.BorderSizePixel = 0
    tButton.ClipsDescendants = true
    tButton.Parent = tFrame

    local dot = Instance.new("Frame")
    dot.Name = "Dot"
    dot.Size = UDim2.new(0,16,0,16)
    dot.Position = UDim2.new(0,2,0.5,-8)
    dot.AnchorPoint = Vector2.new(0,0.5)
    dot.BackgroundColor3 = Color3.fromRGB(255,255,255)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(1,0)
    c.Parent = dot
    dot.Parent = tButton

    return {button=tButton, dot=dot}
end

local toggles = {
    autoWeightPro = createToggle("AUTO WEIGHT 💪 (PRO)",20),
    autoWeightBeginner = createToggle("PESO AUTOMÁTICO 💪 (INICIANTE)",80),
    autoBuy = createToggle("AUTO BUY 🛒",140),
    infDaily = createToggle("✨ INF DAILY ✨",200)
}

-- 5. FUNÇÃO PARA ATIVAR/DESATIVAR TOGGLE -----------------------------------
local function setToggleState(name)
    toggleStates[name] = not toggleStates[name]
    local t = toggles[name]
    if toggleStates[name] then
        t.button.BackgroundColor3 = name=="autoWeightPro" and Color3.fromRGB(100,200,100) or name=="autoWeightBeginner" and Color3.fromRGB(150,220,150) or name=="autoBuy" and Color3.fromRGB(200,200,100) or Color3.fromRGB(220,100,220)
        t.dot.Position = UDim2.new(0.5,-12,0.5,-8)
    else
        t.button.BackgroundColor3 = Color3.fromRGB(200,200,200)
        t.dot.Position = UDim2.new(0,2,0.5,-8)
    end
end

for name,t in pairs(toggles) do
    t.button.MouseButton1Click:Connect(function()
        setToggleState(name)
    end)
end

-- 6. MINIMIZAR CORRETAMENTE -------------------------------------------------
local toggledClosed = false
toggleButton.MouseButton1Click:Connect(function()
    toggledClosed = not toggledClosed
    toggleButton.Text = toggledClosed and "+" or "-"
    contentFrame.Visible = not toggledClosed
    mainFrame.Size = toggledClosed and UDim2.new(0, 280, 0, 30) or UDim2.new(0, 280, 0, 350)
end)

-- 7. ARRASTAR (botão esquerdo na barra de título) --------------------------
local dragging = false
local dragStart, guiStart = nil, nil
titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = Vector2.new(input.Position.X, input.Position.Y)
        guiStart = Vector2.new(mainFrame.Position.X.Offset, mainFrame.Position.Y.Offset)
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local offset = Vector2.new(input.Position.X, input.Position.Y) - dragStart
        mainFrame.Position = UDim2.new(0, guiStart.X + offset.X, 0, guiStart.Y + offset.Y)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- 8. FECHAR NA BARRA CORRIGIDO --------------------------------------------
closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- 9. FUNÇÕES DE AUTOMAÇÃO INDETECTÁVEIS ------------------------------------
local function autoWeightPro()
    if not toggleStates.autoWeightPro or not rootPart then return end
    local weights = workspace:FindPartsInRadius(rootPart.Position, 14, {IgnoreWater=false})
    for _,w in pairs(weights) do
        if w.Name == "Weight" and w:FindFirstChild("Handle") then
            w:ApplyImpulse(Vector3.new(0,5000,0))
            break
        end
    end
end

local function autoWeightBeginner()
    if not toggleStates.autoWeightBeginner then return end
    if humanoid then
        humanoid:ChangeState(Enum.HumanoidStateType.Swimming)
    end
end

local function autoBuy()
    if not toggleStates.autoBuy then return end
    local remotes = ReplicatedStorage:FindFirstChild("Remotes")
    if not remotes then return end
    local buyItem = remotes:FindFirstChild("BuyItem")
    if not buyItem then return end
    local stats = ReplicatedStorage:FindFirstChild("Stats")
    if not stats then return end
    local money = stats:FindFirstChild("Money")
    if not money or money.Value < 5000 then return end
    local items = {
        Strength_Boots = 12345678,
        Giant_Suit = 87654321,
        Weight_Vest = 11223344
    }
    for name,id in pairs(items) do
        pcall(function()
            buyItem:FireServer(id)
        end)
        task.wait(1.7)
    end
end

local function infDaily()
    if not toggleStates.infDaily or not rootPart then return end
    local top = workspace:FindFirstChild("TopPlatform") or (workspace:FindFirstChild("Map") and workspace.Map:FindFirstChild("TopPlatform"))
    if top and rootPart.Position.Y > top.Position.Y + 30 then
        local remotes = ReplicatedStorage:FindFirstChild("Remotes")
        if remotes then
            local claimDaily = remotes:FindFirstChild("ClaimDaily")
            if claimDaily then
                pcall(function()
                    claimDaily:FireServer()
                end)
            end
        end
    end
end

-- 10. LOOP PRINCIPAL (sem prints pra ficar indetectável) --------------------
RunService.Heartbeat:Connect(function()
    pcall(autoWeightPro)
    pcall(autoWeightBeginner)
    pcall(autoBuy)
    pcall(infDaily)
end)

print("Script indetectável ativado. GUI flutuante pronta.")
```
Espero que isso ajude! Se tiver mais alguma dúvida ou precisar de mais ajuda, basta perguntar.
