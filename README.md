if not game:IsLoaded() then game.Loaded:Wait() end

-- Referências
local plr = game.Players.LocalPlayer
local chr = plr.Character or plr.CharacterAdded:Wait()
local hrp = chr:WaitForChild("HumanoidRootPart")

-- Sea Detect
local function detectarSEA()
    local id = game.PlaceId
    if id == 2753915549 then return "Sea 1"
    elseif id == 4442272183 then return "Sea 2"
    elseif id == 7449423635 then return "Sea 3"
    else return "Desconhecido" end
end
local SEA = detectarSEA()

-- Ilhas por SEA
local ilhas = {
    ["Sea 1"] = {
        ["Starter Island"] = CFrame.new(1050, 16, 1425),
        ["Jungle"] = CFrame.new(-1619, 36, 145),
        ["Pirate Village"] = CFrame.new(-1123, 5, 3855),
        ["Desert"] = CFrame.new(1093, 5, 4315),
        ["Snow Island"] = CFrame.new(1354, 27, -1325),
        ["Marine Fortress"] = CFrame.new(-4505, 20, 4260),
        ["Skylands"] = CFrame.new(-7895, 5545, -380),
        ["Prison"] = CFrame.new(5307, 5, 474),
        ["Colosseum"] = CFrame.new(-1994, 11, -2742),
        ["Magma Village"] = CFrame.new(-5246, 8, 8457),
        ["Underwater City"] = CFrame.new(61163, 18, 1540),
        ["Fountain City"] = CFrame.new(5250, 50, 4000),
    },

    ["Sea 2"] = {
        ["Kingdom of Rose"] = CFrame.new(-393, 73, 672),
        ["Green Zone"] = CFrame.new(-2276, 72, -3210),
        ["Graveyard"] = CFrame.new(-5375, 8, -4725),
        ["Snow Mountain"] = CFrame.new(1396, 430, -4811),
        ["Hot and Cold"] = CFrame.new(-5850, 79, -2300),
        ["Cursed Ship"] = CFrame.new(921, 125, 32873),
        ["Ice Castle"] = CFrame.new(-6320, 15, -5815),
        ["Forgotten Island"] = CFrame.new(-3050, 238, -10160),
        ["Usoap's Island"] = CFrame.new(4745, 8, -1926),
    },

    ["Sea 3"] = {
        ["Port Town"] = CFrame.new(-287, 44, 5454),
        ["Hydra Island"] = CFrame.new(5227, 603, 345),
        ["Great Tree"] = CFrame.new(2307, 37, -6453),
        ["Castle on the Sea"] = CFrame.new(-5486, 313, -2859),
        ["Floating Turtle"] = CFrame.new(-11043, 331, -8857),
        ["Haunted Castle"] = CFrame.new(-9515, 142, 5914),
        ["Sea of Treats"] = CFrame.new(-1400, 37, -12000),
        ["Candy Island"] = CFrame.new(-1098, 14, -13300),
        ["Cake Island"] = CFrame.new(-2060, 38, -12460),
        ["Ice Cream Island"] = CFrame.new(-1175, 14, -12280),
    }
}

-- UI Setup
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "BahiaHubUI"
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 300)
frame.Position = UDim2.new(0.5, -150, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "Bahia Hub - " .. SEA
title.TextColor3 = Color3.fromRGB(0, 255, 127)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.BackgroundTransparency = 1

-- Botões ON/OFF
local autoFarmOn = false
local girarFrutaOn = false
local frutasOn = false
local ilhaSelecionada = nil

local function criarBotao(texto, ordem, callback)
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, 50 + (ordem * 35))
    btn.Text = texto
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.Gotham
    btn.TextScaled = true
    btn.MouseButton1Click:Connect(callback)
    return btn
end

criarBotao("🔁 Auto Farm: OFF", 0, function(btn)
    autoFarmOn = not autoFarmOn
    btn.Text = "🔁 Auto Farm: " .. (autoFarmOn and "ON" or "OFF")
end)

criarBotao("🎰 Girar Fruta: OFF", 1, function(btn)
    girarFrutaOn = not girarFrutaOn
    btn.Text = "🎰 Girar Fruta: " .. (girarFrutaOn and "ON" or "OFF")
end)

criarBotao("🍉 Frutas Auto: OFF", 2, function(btn)
    frutasOn = not frutasOn
    btn.Text = "🍉 Frutas Auto: " .. (frutasOn and "ON" or "OFF")
end)

-- Dropdown manual das ilhas
local dropdown = Instance.new("TextButton", frame)
dropdown.Size = UDim2.new(1, -20, 0, 30)
dropdown.Position = UDim2.new(0, 10, 0, 160)
dropdown.Text = "🌴 Selecionar Ilha"
dropdown.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
dropdown.TextColor3 = Color3.new(1,1,1)
dropdown.Font = Enum.Font.Gotham
dropdown.TextScaled = true

local listaIlhas = Instance.new("ScrollingFrame", frame)
listaIlhas.Size = UDim2.new(1, -20, 0, 80)
listaIlhas.Position = UDim2.new(0, 10, 0, 200)
listaIlhas.CanvasSize = UDim2.new(0, 0, 0, 0)
listaIlhas.Visible = false
listaIlhas.BackgroundColor3 = Color3.fromRGB(45,45,45)

dropdown.MouseButton1Click:Connect(function()
    listaIlhas.Visible = not listaIlhas.Visible
end)

local y = 0
for nome, cf in pairs(ilhas[SEA] or {}) do
    local btn = Instance.new("TextButton", listaIlhas)
    btn.Size = UDim2.new(1, 0, 0, 25)
    btn.Position = UDim2.new(0, 0, 0, y)
    btn.Text = nome
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.Gotham
    btn.TextScaled = true
    btn.MouseButton1Click:Connect(function()
        ilhaSelecionada = cf
        dropdown.Text = "🏝️ Ilha: " .. nome
        listaIlhas.Visible = false
    end)
    y += 30
end
listaIlhas.CanvasSize = UDim2.new(0, 0, 0, y)

-- Threads
spawn(function()
    while task.wait(5) do
        if autoFarmOn and ilhaSelecionada then
            hrp.CFrame = ilhaSelecionada + Vector3.new(0, 10, 0)
        end
    end
end)

spawn(function()
    while task.wait(60) do
        if girarFrutaOn and SEA == "Sea 1" then
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Cousin")
            end)
        end
    end
end)

spawn(function()
    while task.wait(5) do
        if frutasOn then
            for _, fruta in pairs(workspace:GetChildren()) do
                if fruta:IsA("Tool") and fruta:FindFirstChild("Handle") then
                    hrp.CFrame = fruta.Handle.CFrame
                    task.wait(1)
                end
            end
            for _, item in pairs(plr.Backpack:GetChildren()) do
                if item:IsA("Tool") and string.find(item.Name, "Fruit") then
                    game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("StoreFruit", item.Name)
                end
            end
        end
    end
end)
