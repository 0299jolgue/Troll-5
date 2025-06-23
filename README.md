--[[
    Jolgue Hub - Roblox executor script
    Features:
    - Minimizar / restaurar hub
    - Aba Principal (girar player rápido, infinite yield, crash particles)
    - Aba Itens (pegar itens da workspace)
--]]

if game.CoreGui:FindFirstChild("JolgueHub") then
    game.CoreGui.JolgueHub:Destroy()
end

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "JolgueHub"
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false

local RestoreButton = Instance.new("TextButton")
RestoreButton.Size = UDim2.new(0, 140, 0, 35)
RestoreButton.Position = UDim2.new(0, 10, 0.5, -20)
RestoreButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
RestoreButton.TextColor3 = Color3.new(1,1,1)
RestoreButton.Font = Enum.Font.GothamBold
RestoreButton.TextSize = 16
RestoreButton.Text = "Abrir Jolgue Hub"
RestoreButton.Visible = false
RestoreButton.Parent = ScreenGui
Instance.new("UICorner", RestoreButton)

local HubFrame = Instance.new("Frame")
HubFrame.Size = UDim2.new(0, 320, 0, 400)
HubFrame.Position = UDim2.new(0.5, -160, 0.5, -200)
HubFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
HubFrame.Parent = ScreenGui
Instance.new("UICorner", HubFrame)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -50, 0, 40)
Title.Position = UDim2.new(0, 10, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Jolgue Hub"
Title.TextColor3 = Color3.new(1,1,1)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 24
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = HubFrame

local btnMinimizar = Instance.new("TextButton")
btnMinimizar.Size = UDim2.new(0, 35, 0, 35)
btnMinimizar.Position = UDim2.new(1, -40, 0, 5)
btnMinimizar.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
btnMinimizar.TextColor3 = Color3.new(1,1,1)
btnMinimizar.Font = Enum.Font.GothamBold
btnMinimizar.TextSize = 20
btnMinimizar.Text = "-"
btnMinimizar.Parent = HubFrame
Instance.new("UICorner", btnMinimizar)

btnMinimizar.MouseButton1Click:Connect(function()
    HubFrame.Visible = false
    RestoreButton.Visible = true
end)

RestoreButton.MouseButton1Click:Connect(function()
    HubFrame.Visible = true
    RestoreButton.Visible = false
end)

local NavFrame = Instance.new("Frame")
NavFrame.Size = UDim2.new(1, -20, 0, 40)
NavFrame.Position = UDim2.new(0, 10, 0, 45)
NavFrame.BackgroundTransparency = 1
NavFrame.Parent = HubFrame

local UIListNav = Instance.new("UIListLayout")
UIListNav.FillDirection = Enum.FillDirection.Horizontal
UIListNav.HorizontalAlignment = Enum.HorizontalAlignment.Left
UIListNav.Padding = UDim.new(0, 10)
UIListNav.Parent = NavFrame

local function criarBotao(nome, parent, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 110, 1, 0)
    btn.Text = nome
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 16
    btn.TextColor3 = Color3.new(1,1,1)
    btn.BackgroundColor3 = Color3.fromRGB(65,65,65)
    btn.Parent = parent
    Instance.new("UICorner", btn)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local MainTab = Instance.new("Frame")
MainTab.Size = UDim2.new(1, -20, 0, 280)
MainTab.Position = UDim2.new(0, 10, 0, 90)
MainTab.BackgroundTransparency = 1
MainTab.Parent = HubFrame

local ItemsTab = Instance.new("Frame")
ItemsTab.Size = UDim2.new(1, -20, 0, 280)
ItemsTab.Position = UDim2.new(0, 10, 0, 90)
ItemsTab.BackgroundTransparency = 1
ItemsTab.Visible = false
ItemsTab.Parent = HubFrame

criarBotao("Principal", NavFrame, function()
    MainTab.Visible = true
    ItemsTab.Visible = false
end)

criarBotao("Itens", NavFrame, function()
    MainTab.Visible = false
    ItemsTab.Visible = true
end)

criarBotao("Fechar", NavFrame, function()
    ScreenGui:Destroy()
end)

local btnGirar = criarBotao("Girar Player", MainTab, function() end)
btnGirar.Position = UDim2.new(0, 0, 0, 0)
btnGirar.Size = UDim2.new(1, 0, 0, 40)

local girar = false
btnGirar.MouseButton1Click:Connect(function()
    girar = not girar
    btnGirar.Text = girar and "Parar Giro" or "Girar Player"
    spawn(function()
        while girar and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") do
            LocalPlayer.Character:SetPrimaryPartCFrame(
                LocalPlayer.Character.PrimaryPart.CFrame * CFrame.Angles(0, 2.094, 0)
            )
            task.wait(1/60)
        end
    end)
end)

local btnInfinite = criarBotao("Infinite Yield", MainTab, function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
end)
btnInfinite.Position = UDim2.new(0, 0, 0, 50)
btnInfinite.Size = UDim2.new(1, 0, 0, 40)
btnInfinite.Parent = MainTab

local btnParticles = criarBotao("Crash Partículas", MainTab, function()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local char = plr.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                local part = Instance.new("Part")
                part.Size = Vector3.new(1,1,1)
                part.Anchored = true
                part.CanCollide = false
                part.Transparency = 1
                part.CFrame = char.HumanoidRootPart.CFrame
                part.Parent = workspace

                local emitter = Instance.new("ParticleEmitter", part)
                emitter.Rate = 1000
                emitter.Lifetime = NumberRange.new(5)
                emitter.Speed = NumberRange.new(50)

                task.delay(10, function() part:Destroy() end)
            end
        end
    end
end)
btnParticles.Position = UDim2.new(0, 0, 0, 100)
btnParticles.Size = UDim2.new(1, 0, 0, 40)
btnParticles.Parent = MainTab

local TitleItens = Instance.new("TextLabel")
TitleItens.Size = UDim2.new(1, 0, 0, 30)
TitleItens.BackgroundTransparency = 1
TitleItens.Text = "Itens disponíveis"
TitleItens.Font = Enum.Font.GothamBold
TitleItens.TextSize = 18
TitleItens.TextColor3 = Color3.new(1,1,1)
TitleItens.Parent = ItemsTab

local UIListItens = Instance.new("UIListLayout")
UIListItens.Padding = UDim.new(0, 8)
UIListItens.SortOrder = Enum.SortOrder.LayoutOrder
UIListItens.Parent = ItemsTab

local function pegarItem(nomeItem)
    local backpack = LocalPlayer.Backpack
    local item = workspace:FindFirstChild(nomeItem)
    if item and item:IsA("Tool") then
        item.Parent = backpack
    else
        warn("Item não encontrado:", nomeItem)
    end
end

local itensExemplo = {"Sword", "Gun"}

for _, nome in ipairs(itensExemplo) do
    local btn = criarBotao(nome, ItemsTab, function()
        pegarItem(nome)
    end)
    btn.Size = UDim2.new(1, 0, 0, 35)
    btn.Parent = ItemsTab
end
