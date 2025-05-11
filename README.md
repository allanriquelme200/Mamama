-- Interface com Aimbot + AutoShoot para jogos com ACS (Delta Executor Mobile)

local Players = game:GetService("Players") local LocalPlayer = Players.LocalPlayer local RunService = game:GetService("RunService") local Camera = workspace.CurrentCamera

local FOV_RADIUS = 100 local TARGET_PART = "Head" local SHOOT_INTERVAL = 0.15 local lastShot = 0 local aiming = false

-- GUI local ScreenGui = Instance.new("ScreenGui", game.CoreGui) local ToggleButton = Instance.new("TextButton") ToggleButton.Size = UDim2.new(0, 120, 0, 40) ToggleButton.Position = UDim2.new(0.5, -60, 0.9, 0) ToggleButton.Text = "Ativar Aimbot" ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30) ToggleButton.TextColor3 = Color3.new(1, 1, 1) ToggleButton.Parent = ScreenGui

-- FOV Circle local fovCircle = Drawing.new("Circle") fovCircle.Radius = FOV_RADIUS fovCircle.Thickness = 2 fovCircle.Color = Color3.fromRGB(255, 0, 0) fovCircle.Filled = false fovCircle.Transparency = 1 fovCircle.Visible = false

-- Toggle Function ToggleButton.MouseButton1Click:Connect(function() aiming = not aiming ToggleButton.Text = aiming and "Desativar Aimbot" or "Ativar Aimbot" fovCircle.Visible = aiming end)

-- Encontra alvo mais próximo dentro do FOV local function getClosestTarget() local closest = nil local shortestDistance = FOV_RADIUS for _, player in ipairs(Players:GetPlayers()) do if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(TARGET_PART) then local part = player.Character:FindFirstChild(TARGET_PART) local pos, onScreen = Camera:WorldToViewportPoint(part.Position) if onScreen then local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)).Magnitude if dist < shortestDistance then shortestDistance = dist closest = part end end end end return closest end

-- Loop Principal RunService.RenderStepped:Connect(function() fovCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

if aiming then
    local target = getClosestTarget()
    if target then
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
        if tick() - lastShot >= SHOOT_INTERVAL then
            local tool = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Tool")
            if tool then
                local fireEvent = tool:FindFirstChild("Fire")
                if fireEvent then
                    fireEvent:FireServer()
                    lastShot = tick()
                end
            end
        end
    end
end

end)

print("[Aimbot ACS] Interface carregada com sucesso.")

