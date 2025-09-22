loadstring(game:HttpGet("https://raw.githubusercontent.com/Totocoems/Ace/main/Ace"))()--// Configuration
getgenv().Enabled = false
getgenv().Speed = 100
getgenv().ESPEnabled = false

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

--// Apply speed function
local function applySpeed()
    local character = LocalPlayer.Character
    if character then
        local humanoid = character:FindFirstChildWhichIsA("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = getgenv().Enabled and getgenv().Speed or 16
        end
    end
end

--// Remove ESP from character's head
local function removeESP(character)
    if not character then return end
    local head = character:FindFirstChild("Head")
    if head then
        local esp = head:FindFirstChild("NameESP")
        if esp then
            esp:Destroy()
        end
    end
end

--// Create ESP for a character
local function createESP(player)
    if player == LocalPlayer then return end
    local character = player.Character
    if not character then return end

    local head = character:FindFirstChild("Head")
    if not head then return end

    -- Avoid duplicate ESP
    if head:FindFirstChild("NameESP") then return end

    local bill = Instance.new("BillboardGui", head)
    bill.Name = "NameESP"
    bill.Size = UDim2.new(0, 100, 0, 30)
    bill.StudsOffset = Vector3.new(0, 2, 0)
    bill.AlwaysOnTop = true
    bill.Adornee = head

    local text = Instance.new("TextLabel", bill)
    text.Size = UDim2.new(1, 0, 1, 0)
    text.BackgroundTransparency = 1
    text.Text = player.Name
    text.TextColor3 = Color3.fromRGB(255, 255, 255)
    text.TextStrokeTransparency = 0.5
    text.Font = Enum.Font.SourceSansBold
    text.TextScaled = true
end

--// Handle ESP on character added and removing on death
local function setupPlayerESP(player)
    -- Remove ESP when character dies
    player.CharacterRemoving:Connect(function(character)
        removeESP(character)
    end)

    -- Create ESP on spawn if ESP enabled
    player.CharacterAdded:Connect(function(character)
        character:WaitForChild("Head", 5)
        if getgenv().ESPEnabled then
            createESP(player)
        end
    end)

    -- If character already exists and ESP enabled, create ESP now
    if player.Character and getgenv().ESPEnabled then
        createESP(player)
    end
end

--// Apply ESP to all players currently in game
for _, player in pairs(Players:GetPlayers()) do
    setupPlayerESP(player)
end

--// Apply ESP to new players joining
Players.PlayerAdded:Connect(function(player)
    setupPlayerESP(player)
end)

--// Update all ESPs according to current toggle state
local function updateAllESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            if getgenv().ESPEnabled then
                createESP(player)
            else
                removeESP(player.Character)
            end
        end
    end
end

--// UI Setup
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "SpeedGUI"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 310, 0, 90)  -- smaller width and height
frame.Position = UDim2.new(0.05, 0, 0.05, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true

-- Speed toggle button
local toggle = Instance.new("TextButton", frame)
toggle.Size = UDim2.new(0, 140, 0, 30)
toggle.Position = UDim2.new(0, 10, 0, 10)
toggle.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
toggle.TextColor3 = Color3.new(1, 1, 1)
toggle.Font = Enum.Font.SourceSansBold
toggle.TextSize = 18
toggle.Text = "Speed: OFF"

-- ESP toggle button next to speed button
local espToggle = Instance.new("TextButton", frame)
espToggle.Size = UDim2.new(0, 140, 0, 30)
espToggle.Position = UDim2.new(0, 160, 0, 10)  -- right next to speed button
espToggle.BackgroundColor3 = Color3.fromRGB(255, 85, 85)
espToggle.TextColor3 = Color3.new(1, 1, 1)
espToggle.Font = Enum.Font.SourceSansBold
espToggle.TextSize = 18
espToggle.Text = "ESP: OFF"

local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0, 290, 0, 30) -- smaller width to fit frame
box.Position = UDim2.new(0, 10, 0, 50)
box.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
box.TextColor3 = Color3.new(1, 1, 1)
box.Font = Enum.Font.SourceSans
box.TextSize = 18
box.Text = tostring(getgenv().Speed)
box.ClearTextOnFocus = false
box.PlaceholderText = "Enter Speed"

--// Speed Toggle Button Clicked
toggle.MouseButton1Click:Connect(function()
	getgenv().Enabled = not getgenv().Enabled
	toggle.Text = getgenv().Enabled and "Speed: ON" or "Speed: OFF"
	applySpeed()
end)

--// ESP Toggle Button Clicked
espToggle.MouseButton1Click:Connect(function()
	getgenv().ESPEnabled = not getgenv().ESPEnabled
	espToggle.Text = getgenv().ESPEnabled and "ESP: ON" or "ESP: OFF"
	updateAllESP()
end)

--// Speed Changed
box.FocusLost:Connect(function()
	local val = tonumber(box.Text)
	if val then
		getgenv().Speed = val
		applySpeed()
	end
end)

--// Loop to maintain speed
task.spawn(function()
	while true do
		task.wait(0.2)
		if getgenv().Enabled then
			applySpeed()
		end
	end
end)
