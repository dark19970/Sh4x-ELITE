--[[
    Sh4x - ELITE SECURITY EDITION
    Features: Staff Check, Raycasting, Tween Bypass, Remote Protection
    Compatibility: MoonSec / Luraph Style
    Security: ANTI-KICK / ANTI-AFK / OBFUSCATION / BYPASS
    Visuals: RED THEME + RGB BORDERS
]]

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local Player = Players.LocalPlayer

-- [STAFF CHECK] Auto-Desconexão se um ADM entrar
local function staffCheck()
    Players.PlayerAdded:Connect(function(player)
        -- Verifica se o jogador que entrou tem cargo de Staff (exemplo comum de IDs ou nomes)
        if player:GetRankInGroup(0) > 100 or player.Name:lower():find("admin") or player.Name:lower():find("mod") then
            Player:Kick("Sh4x Security: Staff detectado no servidor (" .. player.Name .. ")")
        end
    end)
end
task.spawn(staffCheck)

-- [REMOTE SPY PROTECTION] Proteção contra interceptação de remotes
local function protectRemotes()
    if hookmetamethod then
        local oldNamecall
        oldNamecall = hookmetamethod(game, "__namecall", function(self, ...)
            local method = getnamecallmethod()
            if not checkcaller() and (method == "FireServer" or method == "InvokeServer") then
                -- Adiciona um delay randômico para enganar espiões de remotes
                task.wait(math.random(1, 5) / 100)
            end
            return oldNamecall(self, ...)
        end)
    end
end
pcall(protectRemotes)

-- [ANTI-AFK]
Player.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new(0,0))
end)

-- [POLIMORFISMO]
local function generateID()
    return HttpService:GenerateGUID(false):gsub("-", ""):sub(1, 12)
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = generateID()
ScreenGui.Parent = CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = generateID()
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 0, 0)
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -80)
MainFrame.Size = UDim2.new(0, 220, 0, 170)
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 12)

local Stroke = Instance.new("UIStroke", MainFrame)
Stroke.Thickness = 2.5

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Text = "Sh4x ELITE"
Title.TextSize = 16
Title.TextColor3 = Color3.fromRGB(255, 255, 255)

local ToggleBtn = Instance.new("TextButton", MainFrame)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(55, 0, 0)
ToggleBtn.Position = UDim2.new(0.1, 0, 0.3, 0)
ToggleBtn.Size = UDim2.new(0.8, 0, 0, 35)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Text = "START ELITE FARM"
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", ToggleBtn)

local BtnStroke = Instance.new("UIStroke", ToggleBtn)
BtnStroke.Thickness = 1.5

local StatusLabel = Instance.new("TextLabel", MainFrame)
StatusLabel.Position = UDim2.new(0, 0, 0.55, 0)
StatusLabel.Size = UDim2.new(1, 0, 0, 25)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Font = Enum.Font.GothamSemibold
StatusLabel.Text = "SECURITY: ELITE ACTIVE"
StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
StatusLabel.TextSize = 10

local StaffLabel = Instance.new("TextLabel", MainFrame)
StaffLabel.Position = UDim2.new(0, 0, 0.75, 0)
StaffLabel.Size = UDim2.new(1, 0, 0, 25)
StaffLabel.BackgroundTransparency = 1
StaffLabel.Font = Enum.Font.Gotham
StaffLabel.Text = "STAFF SCANNER: ON"
StaffLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StaffLabel.TextSize = 9

-- [RGB LOGIC]
task.spawn(function()
    while true do
        local hue = tick() % 5 / 5
        local color = Color3.fromHSV(hue, 1, 1)
        Stroke.Color = color
        BtnStroke.Color = color
        task.wait()
    end
end)

-- Arrastar
local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
MainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)
game:GetService("UserInputService").InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- ==========================================
-- LÓGICA DE ELITE FARM (TWEEN BYPASS + RAYCAST)
-- ==========================================
local RAIO_OPERACAO = 150
local DISTANCIA_ATAQUE = 8.0
local farmAtivo = false
local lastSwing = 0

ToggleBtn.MouseButton1Click:Connect(function()
    farmAtivo = not farmAtivo
    ToggleBtn.Text = farmAtivo and "ELITE ACTIVE" or "START ELITE FARM"
    ToggleBtn.BackgroundColor3 = farmAtivo and Color3.fromRGB(100, 0, 0) or Color3.fromRGB(55, 0, 0)
end)

local function buscarFerramenta()
    local char = Player.Character
    if not char then return nil end
    local tool = char:FindFirstChildOfClass("Tool")
    if tool then return tool end
    for _, item in pairs(Player.Backpack:GetChildren()) do
        if item:IsA("Tool") then return item end
    end
    return nil
end

local function acharMelhorAlvo()
    local root = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    local alvo = nil
    local menorDist = math.huge
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name == "Pedra" and v:IsA("BasePart") and v.Parent and v.Transparency < 1 then
            local dist = (root.Position - v.Position).Magnitude
            if dist < menorDist and dist <= RAIO_OPERACAO then
                menorDist = dist
                alvo = v
            end
        end
    end
    return alvo
end

-- [RAYCASTING] Validação de linha de visão para evitar kicks de "hit through wall"
local function hasLineOfSight(target)
    local root = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    local rayParams = RaycastParams.new()
    rayParams.FilterDescendantsInstances = {Player.Character}
    rayParams.FilterType = Enum.RaycastFilterType.Blacklist
    local result = workspace:Raycast(root.Position, (target.Position - root.Position), rayParams)
    return result == nil or result.Instance:IsDescendantOf(target.Parent)
end

task.spawn(function()
    while true do
        if farmAtivo then
            local char = Player.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            local tool = buscarFerramenta()

            if root and tool then
                local alvo = acharMelhorAlvo()
                if alvo and hasLineOfSight(alvo) then
                    local dist = (root.Position - alvo.Position).Magnitude
                    
                    if dist > DISTANCIA_ATAQUE then
                        -- [TWEEN BYPASS] Movimentação suave com delay para evitar kicks de velocidade
                        local tweenInfo = TweenInfo.new(dist / 50, Enum.EasingStyle.Linear)
                        local tween = TweenService:Create(root, tweenInfo, {CFrame = CFrame.new(alvo.Position + Vector3.new(0, 3, 0), alvo.Position)})
                        tween:Play()
                        StatusLabel.Text = "TWEENING TO TARGET..."
                        tween.Completed:Wait()
                    else
                        root.Velocity = Vector3.new(0, 0, 0)
                        if tool.Parent ~= char then char.Humanoid:EquipTool(tool) end
                        
                        -- [SMART WAIT] Intervalo inteligente baseado no ping
                        local ping = 50 -- Valor padrão caso não consiga ler stats
                        pcall(function()
                            ping = tonumber(game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValueString():split(" ")[1]) or 50
                        end)
                        local smartWait = 0.25 + (ping / 1000)

                        if tick() - lastSwing > smartWait then
                            lastSwing = tick()
                            tool:Activate()
                            VirtualUser:CaptureController()
                            VirtualUser:ClickButton1(Vector2.new(0, 0))
                            
                            -- Disparo de Remotes Protegido
                            task.spawn(function()
                                for i = 1, 3 do 
                                    for _, r in pairs(ReplicatedStorage:GetDescendants()) do
                                        if r:IsA("RemoteEvent") then
                                            local n = r.Name:lower()
                                            if n:find("mine") or n:find("hit") or n:find("click") then
                                                r:FireServer(alvo)
                                            end
                                        end
                                    end
                                    task.wait(0.05)
                                end
                            end)
                            StatusLabel.Text = "ELITE MINING..."
                        end
                    end
                end
            end
        end
        -- [WAIT INTELIGENTE] Evita sobrecarga e detecção de frequência
        task.wait(0.02)
    end
end)
