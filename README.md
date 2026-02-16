-- VORTEX DOMINATOR V9.1 - VERSÃO FINAL COMPLETA
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local OpenBtn = Instance.new("TextButton")
local Title = Instance.new("TextLabel")
local CloseBtn = Instance.new("TextButton")
local NameInput = Instance.new("TextBox")
local ListFrame = Instance.new("ScrollingFrame")
local UIList = Instance.new("UIListLayout")

-- [ SETUP DA SCREEN ]
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "VortexDominator"

-- [ BOTÃO FLUTUANTE PARA REABRIR ]
OpenBtn.Parent = ScreenGui
OpenBtn.Size = UDim2.new(0, 45, 0, 45)
OpenBtn.Position = UDim2.new(0, 10, 0.4, 0)
OpenBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
OpenBtn.Text = "V"
OpenBtn.TextColor3 = Color3.new(1,1,1)
OpenBtn.Font = Enum.Font.SourceSansBold
OpenBtn.TextSize = 25
OpenBtn.Visible = false
OpenBtn.BorderSizePixel = 0
local Corner = Instance.new("UICorner", OpenBtn)
Corner.CornerRadius = UDim.new(0, 10)

-- [ MENU PRINCIPAL ]
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
MainFrame.BorderSizePixel = 2
MainFrame.BorderColor3 = Color3.fromRGB(138, 43, 226)
MainFrame.Size = UDim2.new(0, 260, 0, 520)
MainFrame.Position = UDim2.new(0.5, -130, 0.2, 0)
MainFrame.Active = true
MainFrame.Draggable = true

Title.Parent = MainFrame; Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "VORTEX DOMINATOR V9"; Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundColor3 = Color3.fromRGB(138, 43, 226); Title.Font = Enum.Font.SourceSansBold

CloseBtn.Parent = MainFrame; CloseBtn.Size = UDim2.new(0, 30, 0, 30); CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.Text = "X"; CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0); CloseBtn.TextColor3 = Color3.new(1,1,1)
CloseBtn.Font = Enum.Font.SourceSansBold

-- [ FUNÇÃO ALTERNAR VISIBILIDADE ]
local function toggleMenu()
    MainFrame.Visible = not MainFrame.Visible
    OpenBtn.Visible = not MainFrame.Visible
end
CloseBtn.MouseButton1Click:Connect(toggleMenu)
OpenBtn.MouseButton1Click:Connect(toggleMenu)

-- [ ELEMENTOS DE INPUT ]
NameInput.Parent = MainFrame; NameInput.PlaceholderText = "Nick do Alvo..."
NameInput.Size = UDim2.new(0.9, 0, 0, 35); NameInput.Position = UDim2.new(0.05, 0, 0.1, 0)
NameInput.BackgroundColor3 = Color3.fromRGB(30, 30, 30); NameInput.TextColor3 = Color3.new(1,1,1)

ListFrame.Parent = MainFrame; ListFrame.Size = UDim2.new(0.9, 0, 0, 120); ListFrame.Position = UDim2.new(0.05, 0, 0.65, 0)
ListFrame.BackgroundColor3 = Color3.fromRGB(5, 5, 5); UIList.Parent = ListFrame

-- [ LÓGICA DE BOTÕES ]
local function createBtn(txt, pos, col)
    local b = Instance.new("TextButton", MainFrame)
    b.Text = txt; b.Size = UDim2.new(0.9, 0, 0, 35); b.Position = pos
    b.BackgroundColor3 = col; b.TextColor3 = Color3.new(1,1,1); b.Font = Enum.Font.SourceSansBold
    return b
end

local escudado = false
local suspeitos = {}

local function SyncServer()
    local char = game.Players.LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        local hum = char:FindFirstChildOfClass("Humanoid")
        hum.Health = 100
        hum:ChangeState(Enum.HumanoidStateType.GettingUp)
        char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame * CFrame.new(0, 0.2, 0)
        hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
    end
end

-- Botão Tag
local TagBtn = createBtn("MARCAR ALVO (TAG)", UDim2.new(0.05, 0, 0.18, 0), Color3.fromRGB(138, 43, 226))
TagBtn.MouseButton1Click:Connect(function()
    local target = NameInput.Text:lower()
    for _, p in pairs(game.Players:GetPlayers()) do
        if string.find(p.Name:lower(), target) and p.Character and p.Character:FindFirstChild("Head") then
            local h = p.Character.Head
            if h:FindFirstChild("V9Tag") then h.V9Tag:Destroy() end
            local b = Instance.new("BillboardGui", h); b.Name = "V9Tag"; b.Size = UDim2.new(0, 200, 0, 50); b.AlwaysOnTop = true
            local t = Instance.new("TextLabel", b); t.Size = UDim2.new(1, 0, 1, 0); t.Text = "ALVO VORTEX"; t.TextColor3 = Color3.new(1,0,0); t.BackgroundTransparency = 1; t.TextScaled = true
        end
    end
end)

-- Botão Radar
local RadarBtn = createBtn("LIGAR RADAR ELITE", UDim2.new(0.05, 0, 0.26, 0), Color3.fromRGB(50, 50, 50))
RadarBtn.MouseButton1Click:Connect(function()
    RadarBtn.Text = "RADAR ATIVO"; RadarBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 0)
    task.spawn(function()
        while task.wait(0.5) do
            for _, p in pairs(game.Players:GetPlayers()) do
                if p ~= game.Players.LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                    if p.Character.HumanoidRootPart.Velocity.Magnitude > 165 and not suspeitos[p.Name] then
                        suspeitos[p.Name] = true
                        local l = Instance.new("TextLabel", ListFrame)
                        l.Size = UDim2.new(1, 0, 0, 25); l.Text = " [!] " .. p.Name; l.TextColor3 = Color3.new(1,1,1); l.BackgroundColor3 = Color3.fromRGB(40,0,0)
                    end
                end
            end
        end
    end)
end)

-- Botão Escudo
local BypassBtn = createBtn("ESCUDO VORTEX: OFF", UDim2.new(0.05, 0, 0.34, 0), Color3.fromRGB(180, 0, 0))
BypassBtn.MouseButton1Click:Connect(function()
    escudado = not escudado
    BypassBtn.Text = escudado and "ESCUDO: ATIVO" or "ESCUDO VORTEX: OFF"
    BypassBtn.BackgroundColor3 = escudado and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(180, 0, 0)
    if escudado then SyncServer() end
end)

-- Botão Fix
local FixBtn = createBtn("SINCRONIZAR / REVIVER", UDim2.new(0.05, 0, 0.42, 0), Color3.fromRGB(0, 150, 150))
FixBtn.MouseButton1Click:Connect(SyncServer)

-- Botão Voo
local FlyBtn = createBtn("ATIVAR VOO FANTASMA", UDim2.new(0.05, 0, 0.50, 0), Color3.fromRGB(0, 80, 200))
FlyBtn.MouseButton1Click:Connect(function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Invinicible-Flight-R15-45414"))()
end)

-- Botão Limpar
local ClearBtn = createBtn("LIMPAR RADAR", UDim2.new(0.05, 0, 0.9, 0), Color3.fromRGB(100, 0, 0))
ClearBtn.Size = UDim2.new(0.9, 0, 0, 25)
ClearBtn.MouseButton1Click:Connect(function()
    for _, c in pairs(ListFrame:GetChildren()) do if c:IsA("TextLabel") then c:Destroy() end end
    suspeitos = {}
end)

-- [ PROTEÇÃO EM SEGUNDO PLANO ]
game:GetService("RunService").Heartbeat:Connect(function()
    if escudado then
        local char = game.Players.LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.BreakJointsOnDeath = false
            if hum.Health <= 0 then
                hum.Health = 0.1
                SyncServer() -- Ressurreição instantânea se detectar morte
            elseif hum.Health < 100 then
                hum.Health = 100
            end
        end
    end
end)
