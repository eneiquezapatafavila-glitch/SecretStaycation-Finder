local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local pg = player.PlayerGui

if pg:FindFirstChild("FrutaFinder") then pg.FrutaFinder:Destroy() end

local items = {
    -- Frutas
    "Apple","Carrot","Orange","Avocado","Banana","Blueberry",
    "Cherry Tomato","Dragon Fruit","Fig","Green Apple","Green Kiwi",
    "Lemon","Lychee","Mango","Mangosteen","Melon","Baby Watermelon",
    "Watermelon","Pear","Persimmon","Pineapple","Strawberry",
    "Shine Muscat","Starfruit",
    -- Verduras
    "Asparagus","Black Olive","Cabbage","Chili Pepper","Cucumber",
    "Eggplant","Green Bell Pepper","Leek","Lotus Root","Mushroom",
    "Onion","Potato","Pumpkin","Salad Leaf","Sweet Potato",
    "Tomato","White Radish",
    -- Dulces y panes
    "Apple Pie","Chocolate Bar","Chocolate Chip Cookie",
    "Chocolate Doughnut","Croissant","French Bread","Gyoza",
    "Kashiwamochi","Melonpan","Mitarashi Dango","Onsen Manju",
    "Pepperoni Pizza","Plain Bread","Rice Ball","Sakuramochi",
    "Sanshoku Dango","Soy Sauce Senbei","Sugar Cube",
    "Tamago Sushi",
    -- Huevos y especiales
    "Egg","Farmer Egg","Butler Egg","Chicken Nugget",
    "Small Piece Of Cheese","Fake Edamame","White Marshmallow",
    "Chocolate Macaron","Pancake","Peanut",
    -- Otros permanentes
    "Caviar","Coconut","Consomme Potato Chip","Empty Sandwich",
    "Omelette Rice","Takoyaki"
}

local function buscarItem(nombre)
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj.Name:lower() == nombre:lower() then
            if obj:IsA("BasePart") or obj:IsA("Model") then
                return obj
            end
        end
    end
    return nil
end

local function getPosicion(obj)
    if obj:IsA("Model") and obj.PrimaryPart then
        return obj.PrimaryPart.Position
    elseif obj:IsA("BasePart") then
        return obj.Position
    end
    return nil
end

local function moverA(pos, nombre, status)
    local char = player.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    if not hrp or not hum then return end
    local speedOrig = hum.WalkSpeed
    hum.WalkSpeed = 80
    status.Text = "🏃 Yendo a "..nombre
    status.TextColor3 = Color3.fromRGB(100,255,150)
    local tw = TweenService:Create(hrp,
        TweenInfo.new(2.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut),
        {CFrame = CFrame.new(pos + Vector3.new(0,3,0))}
    )
    tw:Play()
    tw.Completed:Connect(function()
        hum.WalkSpeed = speedOrig
        status.Text = "✅ Llegaste: "..nombre
        status.TextColor3 = Color3.fromRGB(255,220,50)
    end)
end

-- GUI
local sg = Instance.new("ScreenGui")
sg.Name = "FrutaFinder"
sg.ResetOnSpawn = false
sg.Parent = pg

local main = Instance.new("Frame")
main.Size = UDim2.new(0,220,0,420)
main.Position = UDim2.new(0,10,0.5,-210)
main.BackgroundColor3 = Color3.fromRGB(15,15,25)
main.BorderSizePixel = 0
main.Parent = sg
Instance.new("UICorner",main).CornerRadius = UDim.new(0,12)

local header = Instance.new("Frame")
header.Size = UDim2.new(1,0,0,42)
header.BackgroundColor3 = Color3.fromRGB(210,40,70)
header.BorderSizePixel = 0
header.Parent = main
Instance.new("UICorner",header).CornerRadius = UDim.new(0,12)

local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1,0,1,0)
titulo.BackgroundTransparency = 1
titulo.Text = "🍓 SS Item Finder"
titulo.TextColor3 = Color3.fromRGB(255,255,255)
titulo.TextSize = 13
titulo.Font = Enum.Font.GothamBold
titulo.Parent = header

local drag,ds,sp = false,nil,nil
header.InputBegan:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 then
        drag=true ds=i.Position sp=main.Position end
end)
header.InputChanged:Connect(function(i)
    if drag and i.UserInputType==Enum.UserInputType.MouseMovement then
        local d=i.Position-ds
        main.Position=UDim2.new(sp.X.Scale,sp.X.Offset+d.X,sp.Y.Scale,sp.Y.Offset+d.Y)
    end
end)
header.InputEnded:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 then drag=false end
end)

local status = Instance.new("TextLabel")
status.Size = UDim2.new(1,-16,0,32)
status.Position = UDim2.new(0,8,0,48)
status.BackgroundColor3 = Color3.fromRGB(25,25,40)
status.BorderSizePixel = 0
status.Text = "Presiona Escanear"
status.TextColor3 = Color3.fromRGB(180,220,255)
status.TextSize = 11
status.Font = Enum.Font.Gotham
status.TextWrapped = true
status.Parent = main
Instance.new("UICorner",status).CornerRadius = UDim.new(0,8)

-- Buscador
local searchBox = Instance.new("TextBox")
searchBox.Size = UDim2.new(1,-16,0,28)
searchBox.Position = UDim2.new(0,8,0,86)
searchBox.BackgroundColor3 = Color3.fromRGB(30,30,50)
searchBox.BorderSizePixel = 0
searchBox.Text = ""
searchBox.PlaceholderText = "🔎 Filtrar..."
searchBox.PlaceholderColor3 = Color3.fromRGB(120,120,120)
searchBox.TextColor3 = Color3.fromRGB(255,255,255)
searchBox.TextSize = 11
searchBox.Font = Enum.Font.Gotham
searchBox.ClearTextOnFocus = false
searchBox.Parent = main
Instance.new("UICorner",searchBox).CornerRadius = UDim.new(0,8)

local scanBtn = Instance.new("TextButton")
scanBtn.Size = UDim2.new(1,-16,0,28)
scanBtn.Position = UDim2.new(0,8,0,120)
scanBtn.BackgroundColor3 = Color3.fromRGB(30,100,200)
scanBtn.BorderSizePixel = 0
scanBtn.Text = "🔍 Escanear mapa"
scanBtn.TextColor3 = Color3.fromRGB(255,255,255)
scanBtn.TextSize = 12
scanBtn.Font = Enum.Font.GothamBold
scanBtn.Parent = main
Instance.new("UICorner",scanBtn).CornerRadius = UDim.new(0,8)

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1,-16,1,-158)
scroll.Position = UDim2.new(0,8,0,154)
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 3
scroll.ScrollBarImageColor3 = Color3.fromRGB(210,40,70)
scroll.CanvasSize = UDim2.new(0,0,0,0)
scroll.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0,4)
layout.Parent = scroll

local todosLosItems = {}

local iconos = {
    Apple="🍎", Carrot="🥕", Orange="🍊", Avocado="🥑",
    Banana="🍌", Blueberry="🫐", Mango="🥭", Mangosteen="🍇",
    Melon="🍈", Watermelon="🍉", Pear="🍐", Pineapple="🍍",
    Strawberry="🍓", Lemon="🍋", Coconut="🥥", Tomato="🍅",
    Potato="🥔", Pumpkin="🎃", Mushroom="🍄", Onion="🧅",
    Egg="🥚", Pancake="🥞", Croissant="🥐", Pizza="🍕",
}

local function getIcono(nombre)
    for k,v in pairs(iconos) do
        if nombre:lower():find(k:lower()) then return v end
    end
    return "🍴"
end

local function crearBoton(nombre, pos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,0,0,34)
    btn.BackgroundColor3 = Color3.fromRGB(28,28,45)
    btn.BorderSizePixel = 0
    btn.Text = getIcono(nombre).." "..nombre
    btn.TextColor3 = Color3.fromRGB(235,235,235)
    btn.TextSize = 11
    btn.Font = Enum.Font.Gotham
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.Parent = scroll
    local pad = Instance.new("UIPadding")
    pad.PaddingLeft = UDim.new(0,8)
    pad.Parent = btn
    Instance.new("UICorner",btn).CornerRadius = UDim.new(0,8)

    btn.MouseButton1Click:Connect(function()
        TweenService:Create(btn,TweenInfo.new(0.15),
            {BackgroundColor3=Color3.fromRGB(210,40,70)}):Play()
        task.delay(0.3,function()
            TweenService:Create(btn,TweenInfo.new(0.2),
                {BackgroundColor3=Color3.fromRGB(28,28,45)}):Play()
        end)
        moverA(pos, nombre, status)
    end)
    return btn
end

-- Filtro búsqueda
searchBox:GetPropertyChangedSignal("Text"):Connect(function()
    local q = searchBox.Text:lower()
    for _, c in ipairs(scroll:GetChildren()) do
        if c:IsA("TextButton") then
            c.Visible = q=="" or c.Text:lower():find(q) ~= nil
        end
    end
end)

scanBtn.MouseButton1Click:Connect(function()
    for _, c in ipairs(scroll:GetChildren()) do
        if c:IsA("TextButton") then c:Destroy() end
    end
    todosLosItems = {}
    status.Text = "🔍 Escaneando..."
    status.TextColor3 = Color3.fromRGB(255,200,50)
    searchBox.Text = ""

    task.wait(0.1)
    for _, nombre in ipairs(items) do
        local obj = buscarItem(nombre)
        if obj then
            local pos = getPosicion(obj)
            if pos then
                table.insert(todosLosItems, {n=nombre, p=pos})
                crearBoton(nombre, pos)
            end
        end
    end

    local total = #todosLosItems
    scroll.CanvasSize = UDim2.new(0,0,0,total*38)

    if total==0 then
        status.Text = "❌ Nada encontrado"
        status.TextColor3 = Color3.fromRGB(255,80,80)
    else
        status.Text = "✅ "..total.." items encontrados"
        status.TextColor3 = Color3.fromRGB(100,255,150)
    end
end)

print("✅ SS Item Finder listo")
