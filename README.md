local teleport_table = {
    location1 = Vector3.new(0, 0, 0), -- Primeira parada
    location2 = Vector3.new(0, 1, 0)   -- Ponto final
}

local tween_s = game:GetService('TweenService')
local tweeninfo = TweenInfo.new(2, Enum.EasingStyle.Linear) -- Tempo ajustável

local lp = game.Players.LocalPlayer

function bypass_teleport(v, callback)
    if lp.Character and lp.Character:FindFirstChild('HumanoidRootPart') then
        local cf = CFrame.new(v)
        local a = tween_s:Create(lp.Character.HumanoidRootPart, tweeninfo, {CFrame = cf})
        a:Play()
        
        -- Espera o tween terminar antes de executar o callback (próximo destino)
        a.Completed:Connect(function()
            if callback then callback() end
        end)
    end
end

-- Movendo para a primeira posição e depois para a segunda
bypass_teleport(teleport_table.location1, function()
    bypass_teleport(teleport_table.location2)
end)
