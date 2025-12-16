--// SCRIPT EXECUTADO by DARK

-- Services
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- Player
local LocalPlayer = Players.LocalPlayer

-- Config teclas
local ESP_KEY = Enum.KeyCode.E
local SPEED_KEY = Enum.KeyCode.V
local JUMP_KEY = Enum.KeyCode.B
local NOCLIP_KEY = Enum.KeyCode.N

-- Valores
local SPEED_VALUE = 50
local JUMP_VALUE = 80

-- Estados
local espOn = true
local speedOn = false
local jumpOn = false
local noclipOn = false

--========================
-- GUI INFO (CANTO DA TELA)
--========================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "DarkInfoGui"
ScreenGui.Parent = game.CoreGui

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 260, 0, 145)
Frame.Position = UDim2.new(0, 10, 0.72, 0)
Frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
Frame.BorderSizePixel = 0
Frame.BackgroundTransparency = 0.1

local UICorner = Instance.new("UICorner", Frame)
UICorner.CornerRadius = UDim.new(0, 10)

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, -30, 0, 24)
Title.Position = UDim2.new(0, 8, 0, 4)
Title.BackgroundTransparency = 1
Title.Text = "SCRIPT EXECUTADO by DARK"
Title.TextSize = 14
Title.TextColor3 = Color3.new(1,1,1)
Title.TextXAlignment = Enum.TextXAlignment.Left

local Close = Instance.new("TextButton", Frame)
Close.Size = UDim2.new(0, 20, 0, 20)
Close.Position = UDim2.new(1, -25, 0, 5)
Close.BackgroundTransparency = 1
Close.Text = "X"
Close.TextSize = 14
Close.TextColor3 = Color3.fromRGB(255,80,80)

Close.MouseButton1Click:Connect(function()
	ScreenGui:Destroy()
end)

-- TEXTO DAS FUNÇÕES
local Info = Instance.new("TextLabel")
Info.Parent = Frame
Info.Position = UDim2.new(0, 10, 0, 32)
Info.Size = UDim2.new(1, -20, 0, 105)
Info.BackgroundTransparency = 1
Info.TextColor3 = Color3.fromRGB(220,220,220)
Info.TextSize = 13
Info.Font = Enum.Font.SourceSans
Info.TextXAlignment = Enum.TextXAlignment.Left
Info.TextYAlignment = Enum.TextYAlignment.Top
Info.TextWrapped = true
Info.RichText = true

Info.Text =
	"<b>ESP PLAYER:</b> Tecla <b>E</b>\n" ..
	"<b>VELOCIDADE:</b> Tecla <b>V</b>\n" ..
	"<b>PULO:</b> Tecla <b>B</b>\n" ..
	"<b>NO CLIP:</b> Tecla <b>N</b>"

--========================
-- ESP PLAYER (ATRAVESSA PAREDES DE VERDADE)
--========================
local highlights = {}

local function removeESP(player)
	if highlights[player] then
		highlights[player]:Destroy()
		highlights[player] = nil
	end
end

local function addESP(player)
	if player == LocalPlayer then return end

	local function apply(char)
		removeESP(player)

		local hl = Instance.new("Highlight")
		hl.Name = "DarkESP"
		hl.Adornee = char
		hl.FillColor = Color3.fromRGB(255, 0, 0)
		hl.OutlineColor = Color3.fromRGB(255, 255, 255)
		hl.FillTransparency = 0.5
		hl.OutlineTransparency = 0
		hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
		hl.Enabled = espOn

		-- 🔴 PARTE MAIS IMPORTANTE
		hl.Parent = game.CoreGui

		-- Nome acima da cabeça
		local head = char:WaitForChild("Head", 5)
		if head then
			local bb = Instance.new("BillboardGui")
			bb.Name = "DarkNameESP"
			bb.Adornee = head
			bb.Size = UDim2.new(0, 120, 0, 22)
			bb.StudsOffset = Vector3.new(0, 2.8, 0)
			bb.AlwaysOnTop = true
			bb.Parent = game.CoreGui

			local txt = Instance.new("TextLabel", bb)
			txt.Size = UDim2.new(1, 0, 1, 0)
			txt.BackgroundTransparency = 1
			txt.Text = player.Name
			txt.TextSize = 14
			txt.Font = Enum.Font.SourceSansBold
			txt.TextColor3 = Color3.new(1, 1, 1)
			txt.TextStrokeTransparency = 0
		end

		highlights[player] = hl
	end

	if player.Character then
		apply(player.Character)
	end

	player.CharacterAdded:Connect(apply)
	player.CharacterRemoving:Connect(function()
		removeESP(player)
	end)
end

-- Aplicar ESP nos jogadores existentes
for _, p in ipairs(Players:GetPlayers()) do
	addESP(p)
end

-- Novos jogadores
Players.PlayerAdded:Connect(addESP)
Players.PlayerRemoving:Connect(removeESP)


--========================
-- SPEED & JUMP
--========================
local function updateHumanoid()
	local char = LocalPlayer.Character
	if not char then return end
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return end

	hum.WalkSpeed = speedOn and SPEED_VALUE or 16
	hum.JumpPower = jumpOn and JUMP_VALUE or 50
end

LocalPlayer.CharacterAdded:Connect(function()
	task.wait(1)
	updateHumanoid()
end)

--========================
-- NO CLIP
--========================
RunService.Stepped:Connect(function()
	if noclipOn and LocalPlayer.Character then
		for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end
end)

--========================
-- TECLAS DE ATALHO
--========================
UIS.InputBegan:Connect(function(input, gp)
	if gp then return end

	if input.KeyCode == ESP_KEY then
		espOn = not espOn
		for _, hl in pairs(highlights) do
			if hl then
				hl.Enabled = espOn
			end
		end
	end

	if input.KeyCode == SPEED_KEY then
		speedOn = not speedOn
		updateHumanoid()
	end

	if input.KeyCode == JUMP_KEY then
		jumpOn = not jumpOn
		updateHumanoid()
	end

	if input.KeyCode == NOCLIP_KEY then
		noclipOn = not noclipOn
	end
end)
