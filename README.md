-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local StarterGui = game:GetService("StarterGui")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Esperar o personagem
local function getChar()
	return player.Character or player.CharacterAdded:Wait()
end

-- Criar placas roxas constantemente
task.spawn(function()
	while true do
		local char = getChar()
		local hrp = char:FindFirstChild("HumanoidRootPart")
		if hrp then
			local part = Instance.new("Part")
			part.Size = Vector3.new(2.5, 0.2, 2.5)
			part.Anchored = true
			part.CanCollide = false
			part.Color = Color3.fromRGB(128, 0, 128)
			part.Material = Enum.Material.Neon
			part.CFrame = hrp.CFrame * CFrame.new(0, -3, 0)
			part.Parent = workspace

			-- Efeito de desaparecer
			game:GetService("Debris"):AddItem(part, 2)
			TweenService:Create(part, TweenInfo.new(2), {Transparency = 1, Size = Vector3.new(0, 0.2, 0)}):Play()
		end
		task.wait(0.05) -- 50 milissegundos
	end
end)

-- Criar a Tool "Ender tp"
local function criarTool()
	local tool = Instance.new("Tool")
	tool.Name = "Ender tp"
	tool.RequiresHandle = false
	tool.CanBeDropped = false
	tool.Parent = player.Backpack

	-- Ao clicar, teleporta com efeito
	tool.Activated:Connect(function()
		local char = getChar()
		local hrp = char:FindFirstChild("HumanoidRootPart")
		if not hrp then return end

		local targetPos = mouse.Hit.Position

		-- Criar buraco negro no destino
		local blackHole = Instance.new("Part")
		blackHole.Shape = Enum.PartType.Ball
		blackHole.Size = Vector3.new(3, 3, 3)
		blackHole.Anchored = true
		blackHole.CanCollide = false
		blackHole.Material = Enum.Material.Neon
		blackHole.Color = Color3.fromRGB(0, 0, 0)
		blackHole.CFrame = CFrame.new(targetPos)
		blackHole.Parent = workspace

		local tween = TweenService:Create(blackHole, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
			Size = Vector3.new(6, 6, 6),
			Transparency = 0.2
		})
		tween:Play()

		-- Sucção (efeito visual apenas)
		local beamPart = Instance.new("Part")
		beamPart.Anchored = true
		beamPart.CanCollide = false
		beamPart.Transparency = 1
		beamPart.CFrame = CFrame.new((hrp.Position + targetPos) / 2)
		beamPart.Size = Vector3.new(0.2, 0.2, (hrp.Position - targetPos).Magnitude)
		beamPart.Parent = workspace

		local beam = Instance.new("Beam")
		local a0 = Instance.new("Attachment", hrp)
		local a1 = Instance.new("Attachment", blackHole)
		beam.Attachment0 = a0
		beam.Attachment1 = a1
		beam.Color = ColorSequence.new(Color3.fromRGB(255, 0, 255))
		beam.Width0 = 1
		beam.Width1 = 0.5
		beam.FaceCamera = true
		beam.Parent = hrp

		-- Teleporta após pequena espera
		task.wait(0.3)
		hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 5, 0)) -- levemente acima

		-- Limpeza
		game:GetService("Debris"):AddItem(beamPart, 1)
		game:GetService("Debris"):AddItem(blackHole, 1)
		game:GetService("Debris"):AddItem(beam, 1)
	end)
end

-- Dar a Tool
criarTool()
