-- Leak By ServerSadzz 
-- discord.gg/tskcommunity

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-- ================= GUI =================
local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.Name = "FluxoPvP"
ScreenGui.ResetOnSpawn = false

local Panel = Instance.new("Frame", ScreenGui)
Panel.Size = UDim2.new(0, 320, 0, 220)
Panel.Position = UDim2.new(0.5, -160, 0.5, -110)
Panel.BackgroundColor3 = Color3.fromRGB(15,15,15)
Panel.BackgroundTransparency = 0.35
Panel.Active = true
Panel.Draggable = true

local Stroke = Instance.new("UIStroke", Panel)
Stroke.Thickness = 2

local hue = 0
RunService.RenderStepped:Connect(function()
	hue = (hue + 0.004) % 1
	Stroke.Color = Color3.fromHSV(hue,1,1)
end)

local Title = Instance.new("TextLabel", Panel)
Title.Size = UDim2.new(1,0,0,40)
Title.BackgroundTransparency = 1
Title.Text = "FLUXO PVP"
Title.Font = Enum.Font.GothamBold
Title.TextScaled = true
Title.TextColor3 = Color3.new(1,1,1)

-- ================= BOTÕES =================
local function criarBotao(texto, y)
	local b = Instance.new("TextButton", Panel)
	b.Size = UDim2.new(0.85,0,0,45)
	b.Position = UDim2.new(0.075,0,y,0)
	b.BackgroundColor3 = Color3.fromRGB(30,30,30)
	b.Text = texto
	b.TextScaled = true
	b.Font = Enum.Font.GothamBold
	b.TextColor3 = Color3.new(1,1,1)

	local s = Instance.new("UIStroke", b)
	s.Thickness = 1.5
	return b
end

local DesyncBtn = criarBotao("ATIVAR DESYNC", 0.35)
local UnwalkBtn = criarBotao("ATIVAR UNWALK", 0.62)

-- ================= DESYNC ORIGINAL (INALTERADO) =================
local function ativarDesync()

    local FFlags = {
        GameNetPVHeaderRotationalVelocityZeroCutoffExponent = -5000,
        LargeReplicatorWrite5 = true,
        LargeReplicatorEnabled9 = true,
        AngularVelociryLimit = 360,
        TimestepArbiterVelocityCriteriaThresholdTwoDt = 2147483646,
        S2PhysicsSenderRate = 15000,
        DisableDPIScale = true,
        MaxDataPacketPerSend = 2147483647,
        PhysicsSenderMaxBandwidthBps = 20000,
        TimestepArbiterHumanoidLinearVelThreshold = 21,
        MaxMissedWorldStepsRemembered = -2147483648,
        PlayerHumanoidPropertyUpdateRestrict = true,
        SimDefaultHumanoidTimestepMultiplier = 0,
        StreamJobNOUVolumeLengthCap = 2147483647,
        DebugSendDistInSteps = -2147483648,
        GameNetDontSendRedundantNumTimes = 1,
        CheckPVLinearVelocityIntegrateVsDeltaPositionThresholdPercent = 1,
        CheckPVDifferencesForInterpolationMinVelThresholdStudsPerSecHundredth = 1,
        LargeReplicatorSerializeRead3 = true,
        ReplicationFocusNouExtentsSizeCutoffForPauseStuds = 2147483647,
        CheckPVCachedVelThresholdPercent = 10,
        CheckPVDifferencesForInterpolationMinRotVelThresholdRadsPerSecHundredth = 1,
        GameNetDontSendRedundantDeltaPositionMillionth = 1,
        InterpolationFrameVelocityThresholdMillionth = 5,
        StreamJobNOUVolumeCap = 2147483647,
        InterpolationFrameRotVelocityThresholdMillionth = 5,
        CheckPVCachedRotVelThresholdPercent = 10,
        WorldStepMax = 30,
        InterpolationFramePositionThresholdMillionth = 5,
        TimestepArbiterHumanoidTurningVelThreshold = 1,
        SimOwnedNOUCountThresholdMillionth = 2147483647,
        GameNetPVHeaderLinearVelocityZeroCutoffExponent = -5000,
        NextGenReplicatorEnabledWrite4 = true,
        TimestepArbiterOmegaThou = 1073741823,
        MaxAcceptableUpdateDelay = 1,
        LargeReplicatorSerializeWrite4 = true
    }

    local function respawnar(plr)
        local rcdEnabled = false
        if gethidden then
            rcdEnabled = gethidden(workspace,'RejectCharacterDeletions')
                ~= Enum.RejectCharacterDeletions.Disabled
        end

        if rcdEnabled and replicatesignal then
            replicatesignal(plr.ConnectDiedSignalBackend)
            task.wait(Players.RespawnTime - 0.1)
            replicatesignal(plr.Kill)
        else
            local char = plr.Character
            local hum = char:FindFirstChildWhichIsA('Humanoid')
            if hum then
                hum:ChangeState(Enum.HumanoidStateType.Dead)
            end
            char:ClearAllChildren()
            local newChar = Instance.new('Model')
            newChar.Parent = workspace
            plr.Character = newChar
            task.wait()
            plr.Character = char
            newChar:Destroy()
        end
    end

    for n,v in pairs(FFlags) do
        pcall(function()
            setfflag(tostring(n), tostring(v))
        end)
    end

    respawnar(player)
end

-- ================= UNWALK (FUNCIONAL) =================
local unwalk = false
local animConnection

local function bloquearAnimacoes(hum)
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then return end

	for _,track in pairs(animator:GetPlayingAnimationTracks()) do
		track:Stop(0)
	end

	animConnection = animator.AnimationPlayed:Connect(function(track)
		track:Stop(0)
	end)
end

local function liberarAnimacoes()
	if animConnection then
		animConnection:Disconnect()
		animConnection = nil
	end
end

UnwalkBtn.MouseButton1Click:Connect(function()
	unwalk = not unwalk
	local char = player.Character
	local hum = char and char:FindFirstChildOfClass("Humanoid")
	if not hum then return end

	if unwalk then
		hum.WalkSpeed = 16
		hum.AutoRotate = true
		bloquearAnimacoes(hum)
		UnwalkBtn.Text = "UNWALK ATIVO"
	else
		hum.WalkSpeed = 16
		liberarAnimacoes()
		UnwalkBtn.Text = "ATIVAR UNWALK"
	end
end)

-- ================= CLIQUE DESYNC =================
DesyncBtn.MouseButton1Click:Connect(function()
	DesyncBtn.Text = "DESYNC ATIVO"
	DesyncBtn.AutoButtonColor = false
	ativarDesync()
end)
